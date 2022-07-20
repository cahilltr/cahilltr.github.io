---
layout: post
title:  "Spark Unit Functions causes Serialization results bigger than spark.driver.maxResultSize Error"
date:   2022-07-20 12:00:00 -0500
tags:
- Spark
- Scala
- Data
- Debugging
---

Recently, while trying to debug an error during my on-call rotation, I ran into a situation where a [foreachPartition](https://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/Dataset.html#foreachPartition-org.apache.spark.api.java.function.ForeachPartitionFunction-) was causing a [Serialization results bigger than spark.driver.maxResultSize](https://stackoverflow.com/questions/47996396/total-size-of-serialized-results-of-16-tasks-1048-5-mb-is-bigger-than-spark-dr).  The error is caused by too much data being pulled back to the driver and is normally associated with a [collect](https://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/Dataset.html#collect--) function.  But the stacktrace pointed to the `foreachPartition` in our code base, so I struggled to fix the issue.  The documentation says it's a Unit function, so nothing should be returned back to the driver.  But, when I dug into the Spark source code I found the issue.

## Spark Source Code

Diving into the Spark source code, I was eventually lead to a [SparkContext runJob function](https://github.com/apache/spark/blob/v3.0.1/core/src/main/scala/org/apache/spark/SparkContext.scala#L2115), which clearly shows where the results are accumulated. This function, while not the final `runJob` function, nor [the function that submits the job](https://github.com/apache/spark/blob/v3.0.1/core/src/main/scala/org/apache/spark/scheduler/DAGScheduler.scala#L704), it does clearly show how the results are accumulated.

But wait, if the function used in the `foreachPartition` call returns a Unit then how does this cause a "bigger than spark.driver.maxResultSize" error?  The answer is that Scala's `Unit` is still an object, which takes up memory.  So, given enough partitions, you'll eventually consume enough memory to cause an error.

## Reproducing the issue

I was able to easily reproduce the issue in below (and also in this [gist](https://gist.github.com/cahilltr/e9b284db4b8564eaab840f0cc79bb895)).  

{% highlight scala %}
import org.apache.spark.sql.{Row, SparkSession}

object TestApp {

  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Test")
      .master("local[*]")
      .config("spark.driver.maxResultSize", "1b")
      .getOrCreate()

    import spark.implicits._

    val testDF = Range(0, 1000)
      .toDF("id")
      .repartition(999)

    testDF.foreachPartition{ i: Iterator[Row] => println()}
  }
}
{% endhighlight %}

By setting the `spark.driver.maxResultSize = 1b` even [count](https://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/Dataset.html#count--) fails.

Of course setting `spark.driver.maxResultSize = 1b` is ridiculous, but it makes the behavior easy to reproduce.

## Conclusion

Even when using a DataFrame Unit function like `foreachPartition` or `foreach` it _is_ still possible to get a "bigger than spark.driver.maxResultSize" error.  I'm not sure if this behavior is expected behavior or if it's actually a bug.  And after searching Jira, it doesn't seem like anyone has logged a ticket about this behavior.
