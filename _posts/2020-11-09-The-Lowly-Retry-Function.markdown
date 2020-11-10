---
layout: post
title:  "The Lowly Retry Function"
date:   2020-11-09 07:40:00 -0500
tags:
- scala
- retry
- programming
---

# The Lowly Retry Function

The lowly retry function plays an imperative part in the day to day of our operations.  Preventing complete failures when non-determinant errors occur (e.g. database connection failure, throttling of an endpoint, IOExceptions, etc) by simply retrying saves us untold amounts of effort.  Should a failure happen in the middle or at the end of a process, we'd need a separate process to correct the failure, taking on-call time and human intervention.  If a failure happens at the start of a process, you can just restart the process, but the human intervention still remains.

But having to interrupt a developer, either during their work day, which can hinder a meeting or stop development or during their off hours on such an easily correctable issue should be considered an oversight at best and shameful at worse.

Having foresight to use a retry function can be difficult; adding retry functions as areas of code are found to fail indiscriminately should be standard. These failures will probably appear as obvious candidates for a retry function when they occur.  Not adding a retry function when a troublesome area of code has been identified is throwing your team members under the bus in the future.  Adding an undue effort to that persons day that could have been prevented.  It's similar to seeing that you're out of paper towels and instead of getting a new roll, you get yourself a single sheet.

Some libraries should have retry functionality added as an option from the start.  For example, we have a Scala JDBC wrapper that makes our JDBC operations more functional.  Until recently, a connection retry function had not been incorporated into the library.  When we started to notice connection errors causing us problems, that would be resolved by simply retrying, we decided that the connection function of our databases should by default be wrapped in a retry function.  Now, we're able to allow for retrying of connections by default for all database connections.  Since, we've not had a database connection issue prevent the completion of a process.

## Breaking down the retry function

```Scala
final def retryEitherWithFailure[T](
      n: Int
  )(tryFn: => ThrowableOr[T])(failureFn: => Unit): ThrowableOr[T] =
    tryFn match {
      case Right(t) => Right(t)
      case Left(_) if n > 1 =>
        failureFn
        retryEitherWithFailure(n - 1)(tryFn)(failureFn)
      case Left(e) => Left(e)
    }
```

The retry function above is what we use at XMode.  We have a few variations of it, but all variations lead to this function.  There are 3 arguments for this powerful function.  The first argument is `n`; an Int value of the number of retries to attempt.  The next argument is the function that we will retry.  The final function being the failure function or what should be done in the event of a failure.  Most of the time we use the retry function, we add a backoff sleep.  

The second argument `tryFn` will be attempted first. On success, `T` returns.  On failure, the `failureFn` argument runs and we recurse into the function again, but with `n - 1`. With `n` failures, we'll then return `Throwable` as the `tryFn` function has failed and we're out of retries.

Ten lines of code.  Ten lines of code saves us worry, work, and interruptions.

## Summary

The simplicity of the retry function allows the function to do it's job.  And by just doing it's job, the value added becomes greater than it's actual work.
