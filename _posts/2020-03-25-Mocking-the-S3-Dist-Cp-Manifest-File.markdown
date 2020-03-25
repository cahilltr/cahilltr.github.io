---
layout: post
title: "Mocking the S3-Dist-Cp Manifest File"
date: 2020-03-25 01:00:00 -0500
tags:
- AWS
- EMR
- S3DistCP
- Manifest
- Hadoop
---

Recently at XMode, we had an instance where we needed to point an existing S3DistCP job to another input location.  In doing this, we needed to ignore the data that had been written to the new input location so that we would prevent duplicate data from being processed.  To do this, we needed to mock the manifest file that the S3DistCP process uses, which will prevent copying of data that would cause duplication.

# Manifest File Format

The manifest file format consists of a line of JSON per file. The JSON looks like
```JSON
{"path":"hdfs:/output_location/name_of_file_with_any_prefixes", "baseName":"name_of_file_with_any_prefixes", "srcDir":"hdfs:/output_location", "size":100}

{"path":"hdfs:/input/2020-02-21/data.csv.gz", "baseName":"2020-02-21/data.csv.gz", "srcDir":"hdfs:/input", "size":100}
```

## JSON Fields
- `baseName`: The name of the file, along with any prefixes, e.g. if the fully qualified path name is `s3://input/2020-02-02/data.csv` and the input parameter to the S3DistCP job is `s3://input/`, then the basename will be `2020-02-02/data.csv`.
- `srcDir`: The output parameter of the S3DistCP job.
- `path`: The output path with the filename; a concatenation of the `srcDir` field and the `baseName` field.
- `size`: Size of the file

# Mocking the Manifest file

To create the mocked manifest file, we created a small Scala app, which ran an S3 list of the new data source, got the filename and size, and then applied the filename/size using string interpolation to create the line of JSON for that S3 file and appending to the output mocked manifest file.

# Putting the Mocked Manifest into Production

To apply this to production, we concatenated the existing manifest file with the mocked manifest file and simply changed the input path of the S3DistCP command.

# Summary

Mocking the manifest file proved somewhat difficult as no resource could be found on the format of the manifest file itself.  To determine how the manifest file was created, I ran a small S3DistCP job and derived the field definition from that output manifest file.  Finally, I tested my work by running an S3DistCP job, intentionally leaving a few files to actually be copied.
