---
layout: post
title:  "AWS Lambda and the META-INF Folder"
date:   2018-12-28 12:00:00 -0500
tags:
- AWS
- Lambda
- Java
- Scala
- META-INF
---

### Background
Recently I was working on an AWS Lambda that required the ability to get a version of the deployment.

During our build process, we add the Implementation-Version to the MANIFEST file with our internal version.  The most straight forward version of this was:

{% highlight scala %}
this.getClass.getPackage.getImplementationVersion
{% endhighlight %}

### Problem and some attempted solutions
This worked on my (and other's) local JDK.  After doing some searching, I found other's who had similar issues with reading information from their MANIFEST; there was a post on [StackOverflow](https://stackoverflow.com/questions/35639561/aws-lambda-access-to-meta-inf-manifest-mf) and the [Amazon Forums](https://forums.aws.amazon.com/thread.jspa?threadID=226233)

So I decided to file a support ticket with Amazon.  But while waiting, I decided to try and find some other solutions. Other solutions (found from StackOverflow posts) included reading the file directly by pointing to the Manifest file and others that included writing a file into the resources directory (e.g. src/main/resources/MANIFEST.MF).  I was not a fan of this solution as using the getImplementationVersion method was very clean.  A coworker suggested to use the [jcabi-manifests](https://manifests.jcabi.com/) library.  I used this temporary solution as the implementation was nearly as clean as the getImplementationVersion method.

{% highlight java %}
val version: String = Manifests.read("Implementation-Version")
{% endhighlight %}

Using the jcabi-manifests solution worked locally and on AWS Lambda; so this solution will work and solved the problem I had.  But it's not the solution I ended up going with, which we'll come back to later on here.

### AWS Provides some Insight
After some quick back and forth, I learned the quite a bit about Lambda.  When loading a Jar file to Lambda, I was told that the jar is disassembled, with the following distribution -

- class files in /var/task/\<package\>
- libraries in /var/task/lib
- properties and other config files in /var/task/resources

My AWS correspondent then told me

{% highlight quote %}
I wrote a test Lambda function to extract the contents under /var/task 
to check for what content lambda stores on the cloud environment when 
it explodes the supplied deployment package(.jar file in our case).

From the above investigation, I observed that Lambda doesn't avail 
META-INF(where manifest files are stored) folder on the container under 
any path that is accessible during runtime. Hence, the lambda function 
is unavailable to read the manifest file and read for its contents.
{% endhighlight %}

#### AWS Work-Around
So Lambda completely ignores the META-INF folder when unpacking the jar.  Luckily, our AWS correspondent did give a work-around. It was suggested to (assuming Maven is being used), use the Shade plugin to create a Manifest in a custom location. Then during the jar build, the shaded plugin will copy the content of the manifest file to the actual manifest.mf. Next, add a custom manifest.mf file under src/main/resources. This file will contain the contents that are to be copied.  This works because when the jar is unpacked, the manifest.mf file will be extracted to /var/task/resources and can be accessed by using the absolute path.  This solution was talked about earlier and I was not a fan of the solution.

### Final Solution
My final solution included using the Lambda Environment variable "AWS_LAMBDA_FUNCTION_VERSION" which returns the Lambda version number.  We decided to use this version as our exposed version number because it prevents our internal versioning system from possibly being exposed.

### Take Away
The biggest learning point of this adventure is that Lambda does not extract the META-INF folder anywhere when unpacking the jar file.
