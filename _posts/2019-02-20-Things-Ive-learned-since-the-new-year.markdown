---
layout: post
title:  "Things I've Learned Since the New Year"
date:   2019-02-20 07:40:00 -0500
tags:
- Bash
- Scala
- Intellij
---

In the past month and a half, I've bookmarked a quite a few resources as I've continued to learn more about Scala and functional programming.  Basically, I need to clean out my bookmarks bar and thought that the things I've learned would be useful to others.

## Scala

### Escape a dollar sign in string interpolation
To start,

{% highlight scala %}
s"this is a string with a $variable"
{% endhighlight %}

is string interpolation.  Before researching how to have "$" as part of the output string, I had no idea that this was known as string interpolation.

[StackOverflow](https://stackoverflow.com/questions/16875530/escape-a-dollar-sign-in-string-interpolation) was helpful in finding the solution that was the first answer and very precise.

{% highlight scala %}
scala> val name = "foo"
name: String = foo

scala> s"my.package.$name$$"
res0: String = my.package.foo$
{% endhighlight %}

While this isn't a common need for myself, it's a good piece of knowledge to keep in my back pocket.


### Retries, The Scala Away
As I was working with database connections, we found times when the connection would be closed due to timeouts, etc.  This was unplanned as the process was supposed to be quick enough to avoid the issue, but we found in some instances, the timeouts would occur.  So I was tasked with how to prevent failures due to connection timeouts, i.e. handling retries.

[StackOverflow](https://stackoverflow.com/questions/7930814/whats-the-scala-way-to-implement-a-retry-able-call-like-this-one) was useful once again in this process.  I adapted the first answer to create

{% highlight scala %}
def retry[T](n: Int)(fn: => T): Either[Throwable, T] = {
  try {
    Right(fn)
  } catch {
    case e: Throwable =>
      if (n > 1)
        retry(n - 1)(fn)
      else
        Left(e)
  }
}
{% endhighlight %}

The only difference is that I've wrapped the result or a Throwable in an Either.

Using this retry result, we were able to prevent nearly all failures that our task was having.


### Scala's 'By name parameters'
If you look at the above retry method, you may notice the header

{% highlight scala %}
def retry[T](n: Int)(fn: => T): Either[Throwable, T]
{% endhighlight %}

Specifically, that it specifies the input function as "(fn: => T)".  This brought up some questions during the code review and ended up being a great learning experience for everyone.

At the time, I didn't know what I had done (aka the dangers of copy and paste from SO), but it turns out that passing a function as "(fn: => T)", is the Scala way of passing 'by name parameters'.  This means that the function will only be ran when specified to, rather than executing and sending the resulting value into the function.  While, necessary for the retry logic to work this method of passing in a function has other uses especially in a functional programming environment.  For example, it provides the ability to pass in multiple functions, functions that may be slow or have side effects, into a method to be used as needed.

{% highlight scala %}
def aFunction[T](n: Int)(conditionFn: => T, fn: => T, fn2: => T): T = {
  if (conditionFn)
    fn
  else
    fn2
}
{% endhighlight %}

 which results in fn or fn2 being executed, but not both.

 [Here is the StackOverflow explanation.](https://stackoverflow.com/questions/14763591/scala-colon-right-arrow-key-vs-only-colon)

 [And here's generic answer to what's the difference between pass by value and pass by reference.](https://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value)


### Returning NONE, when a string is empty
I wanted to filter out empty strings to put them into an Option.

{% highlight scala %}
Option(s).filter(\_.trim.nonEmpty)
{% endhighlight %}

Just a nice, concise way to accomplish this.  And once again, thanks to [StackOverflow](https://stackoverflow.com/questions/8534172/does-scala-have-a-library-method-to-build-option-s-that-takes-into-account-empty)


### Scala mocking a shutdown method that returns Unit
Recently, when working with AWS SimpleEmailService client, I needed to mock the "shutdown" method.  The [shutdown method](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/simpleemail/AmazonSimpleEmailServiceClient.html#shutdown--) takes no arguments and returns nothing.  I'd never mocked a function that takes not arguments and returns nothing.  Luckily, [StackOverflow](https://stackoverflow.com/questions/34031587/scalamock-3-mock-overloaded-method-without-parameter) came to the rescue again.

{% highlight scala %}
val client = mock[AmazonSimpleEmailService]

// ...other stuff

(client.shutdown: () => Unit)
  .expects()
{% endhighlight %}

was just the solution that I needed.  The only downside of using this technique, is that know, there is a warning

{% highlight text %}
Eta-expansion of zero-argument method values is deprecated. Did you intend to write client.shutdown()?
(client.shutdown: () => Unit)
{% endhighlight %}

Luckily, a quick search and a [ScalaMock issue](https://github.com/paulbutcher/ScalaMock/issues/224), lead me to the solution

{% highlight scala %}
//... same stuff as before
(() => client.shutdown())
  .expects()
{% endhighlight %}

which removes the warning.  The ScalaMock issue does remind us that ScalaMock 3.6 is deprecated and 4.1 should be used.


### Scala Try to Either or Option
Scala's Try has a neat functionality to convert the result of the Try block into and Either or an Option.

{% highlight scala %}
import scala.util.Try

Try {
  function_that_throws_exception()
}.toEither

Try {
  function_that_returns_value()
}.toOption
{% endhighlight %}

After the team learned about this from a code review, we've used it extensively in our code base.


### Scala Tuples
[Tuples](https://ralphcollett.com/2016/01/29/tupled-to-pass-tuples-to-functions-in-scala/) "can be use to convert a function which takes multiple parameters to accept just the tuple keeping the code more concise."  The article shows a great example.  Unfortunately, I've only gotten to use this once in practice.


## Bash

### Making a "beep" sound
While developing and running longer builds, I'll often get distracted and forget about a build.  To help prevent myself from getting side tracked, I wanted a way to "beep" my computer from the command line when a build was done.

{% highlight bash %}
# makes the beep sound
echo -e "\a"

# How I've integrated it into my workflow
gradle clean build; echo -e "\a";

mvn clean package; echo -e "\a";
{% endhighlight %}

[SuperUser](https://superuser.com/questions/598783/play-sound-on-mac-terminal) came in clutch to solve this problem.


### && command to push
In the same vein as making a "beep" sound, many times I want to push after I've built one final time.

{% highlight bash %}
gradle clean build && git add ./; git commit -m "..": git push;
{% endhighlight %}

I've found this combination fantastic when I'm about to transition at work.


## Neat Articles I've read

### Open Source collaboration, rather than internal solutions
[Steve Loughran](https://steveloughran.blogspot.com/2018/12/isolation-is-not-participation.html) gives some good insight into why Open Source collaboration can be considered better than developing an internal solution.


### Intellij Debugging Tips
[Baeldung](https://www.baeldung.com/intellij-debugging-tricks) has some great tips to up your debugging game.  The tip that stood out to me the most was the "Drop Frame" technique, which basically takes you back up the stack.  A great feature considering how often I run by what I was actually wanting to debug.


### Benchmarking Scala Collections
[Benchmarking Scala Collections](http://www.lihaoyi.com/post/BenchmarkingScalaCollections.html) is a great review of the performance of Scala collections.  A good read and a better reference.


### Comparison of database column types in MySQL, PostgreSQL, and SQLite
[StackOverflow](https://stackoverflow.com/questions/1942586/comparison-of-database-column-types-in-mysql-postgresql-and-sqlite-cross-map) has a question that ended up turning into another solid reference piece.


### How Much of the Internet Is Fake?
[Everything on the internet is fake](https://nymag.com/intelligencer/2018/12/how-much-of-the-internet-is-fake.html). Or almost everything.  

## Conclusion
After ending on an "everything is fake" note, it's probably a good idea to [go outside on a hike](https://www.alltrails.com/). It'll be real.
