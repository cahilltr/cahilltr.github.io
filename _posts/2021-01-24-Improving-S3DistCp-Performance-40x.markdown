---
layout: post
title: "Improving on AWS's S3DistCP Performance by 40X"
date: 2021-01-24 07:40:00 -0500
tags:
- aws
- emr
- spark
- scala
- functional-programming
- performance
---

At my current position, we pull data from external S3 buckets for processing. When these data sets are not well partitioned (e.g. dumped into a single prefix), we only want to pull the new and updated files.  This leads to the team using the [manifest file](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/UsingEMR_s3distcp.html) in order to specify what files to exclude from the copy.  As the manifest file grows, the S3DistCP process takes more and more time in the driver, calculating what files should be pulled.  At it's worse, this caused one of our S3DistCP commands to take 40 minutes determining what files to copy and only a minute to do the actual copying.  This means that the EMR clusters that S3DistCP were being run on, were sitting idly by, racking up charges while doing no work. So I set out to create a better solution.

# Replacement Requirements for S3DistCP

As I started planning alternatives to S3DistCP, there were a few points I had to keep in mind
- finding all new files needed to be read once
- the previously read files needed to be recorded
- this problem probably falls under the "embarrassingly parallel" problem

With these key points in mind, I proceeded to work on the problem.

## Listing Objects using startsAfter Argument

### Finding the new files

In one data set that we pull, I noticed that the files were named with the timestamp that they were written in the file name; for example, `data-2021-01-11-03-13-58-928-...`. This meant that the names of the files were in alphabetical order.  After browsing the S3 api documentation, I came across a flag in the [listObjectsV2](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-objects-v2.html) documentation.  The `--start-after` flag, which has the description

{% highlight text %}
StartAfter is where you want Amazon S3 to start listing from.
Amazon S3 starts listing after this specified key.
StartAfter can be any key in the bucket.
{% endhighlight %}

Since the file names were in alphabetical order, this meant that the `--startAfter` flag could be leveraged to only list new files, by specifying the last file that had been seen.  This left 2 problems: how to record the last file seen and how to parallelize the copy.  

### Recording Previously Read Files
At the time, we were under a time crunch to get this process back on track, so I followed the previous idea of a manifest file.  Except in this manifest file only the last seen file and a timestamp associated are written, meaning that very little data gets read/written during each run.  I had thought about using a database and looking back, a database probably provides the most robust solution, but due to the time crunch and my laziness in not wanting to create a new table, I went with a "manifest" file that lives on S3.  Luckily, it would not take much work to move the manifest file to a database.


### Parallelization

To solve the parallelization problem, I used Spark to create RDDs of file names to copy and then ran a `foreachPartition` on the RDDs, which in turn iterated the elements of the RDD (the file names) and copied the files to their destination.  At the time of this writing, I forget specifically how much time was saved, but I recall it being around 50 minutes.

### Result

The result was that the S3DistCP job went from ~50 minutes to ~2 minutes.


## Using the Object Last Modified Date

### Finding New Files and Parallelization

About 9 months later, in another data set we pull, we again encountered performance issues where the manifest file had grown too large.  This data set did not have the same properties of the previous data set, so it required a different solution.  Again, I started by browsing the listObjectsV2 and was not finding anything useful.  So after diving into the decompiled S3DistCP code (thanks Intellij) and seeing that S3DistCP does a listObjectsV2, I realized that this could be parallelized with ease.

To parallelize the listing of objects, I created a table of every 2 letter/number combination.  Then that list of 2 letter combination, once again, becomes split and parallelized by Spark via RDDs. Within each RDD, it was possible to use the `--prefix` flag to only return the files that started with the 2 letter combination of the RDD.  I played around with different size of letter combinations, but found that the 2 letter combinations worked best to prevent S3 throttling and spreading the work (as always, YMMV).  

Also, as a reminder to the reader that S3, being a object store, S3 does not have a file structure and any file structure has been artificially created via the prefix string.  Meaning that the `--prefix` argument essentially becomes a string `startsWith` function for the list objects call. That being said, this process does list _all_ objects with the prefix, meaning that _all_ files in the bucket are listed, but the cost of listing all the objects is far lower than letting an EMR cluster sit idle. As an aside, if a bucket has prefixes that contains separate data sets, e.g.:

{% highlight text %}
s3://myBucket/datasetName1/
s3://myBucket/datasetName2/
s3://myBucket/datasetName3/
...
s3://myBucket/datasetNameN/
{% endhighlight %}

you could simply suffix the 2 letter combination with the dataset name to create the prefix (as seen in the example like `datasetName1/AA, datasetName1/AB, datasetName1/AC, ...`).  But in our case, the files are simply dropped into a bucket.

As the files are read, they are filtered by using a `lastRead` timestamp and filtering on the objects' `lastModified` date. Only objects after the `lastRead` timestamp are then passed on to be read into a dataframe.  The filter operation occurs on a `List`, which means in Scala, that doing things in parallel means just adding `.par`.

This data set happens to be JSON, so we're able to easily convert the JSON into a dataframe for writing later.  The code below shows all of this in action.

{% highlight scala %}
def copyObjectsIntoDataframe(bucket: String, prefixList: Seq[String], filterEpochMillis: Long, schema: StructType, spark: SparkSession): DataFrame = {
 val prefixRead: RDD[String] = spark.sparkContext.parallelize(prefixList)
 import spark.implicits._
 prefixRead.flatMap(prefix => {
  logger.info(s"Getting objects for prefix: $prefix")
	// Function to return listed objects as an Either[Throwable, List[S3ObjectSummary]]
  listObjects(bucket, prefix, recursive = true).fold(
     throw _,
     l =>
     l.par
      .filter(
       objectSummary => objectSummary.getLastModified.toInstant.toEpochMilli > filterEpochMillis
      )
    ).par
     .map(s3ObjectSummary => {
			 //Returns Either[Throwable, S3Object]
       getObject(bucket, s3ObjectSummary.getKey)
        .fold(
         throw _,
         s3Object =>  IOUtils.toString(s3Object.getObjectContent) //reads to string
       )
    }).seq
 }).toDF("value")
   .withColumn("value", from_json($"value", schema)).select($"value.*")
}
{% endhighlight %}  

### Recording Previously Read Files

Keeping with the manifest file idea, we write out the timestamp before the call to the above `copyObjectsIntoDataframe` to a file in S3 and read that file back to be used for filtering.  We create that timestamp to be written before the function because we want to get all the new files without missing any.  An edge case exists where we could read in the same file twice, but that's handled down stream and therefore not an issue.

### Result

The result was that the S3DistCP job went from ~40 minutes to ~1 minute.  A 40X improvement.

# Summary

S3DistCP is a fantastic tool for copying data from point A to point B and we still use S3DistCP.  But any time there's a need to keep track of files, we try to avoid the use of a manifest file as it's shown to have performance issues at scale (scale in this case, being time passed).

In both of the above cases, it seems that we were able to get a ~40X improvement in performance by writing our own processes.  The 40X seems to be consistent because that's when the team starts to notice that clusters are starting to run too long and our monitoring starts alerting every time the process runs.  

If you're planning on using S3DistCP with a manifest for long periods of time or for many files, I'd advice rolling your own or using the `--copyFromManifest`, which only copies the files specified.
