---

title: Automatic Retry and Circuit Breaker made easy
date: 2013-08-07T20:54:09+01:00


guid: http://www.lybecker.com/blog/?p=1100
permalink: /blog/2013/08/07/automatic-retry-and-circuit-breaker-made-easy/
dsq_thread_id:
  - "3456314572"
  - "3456314572"
categories:
  - .Net
  - Everyday coding
  - Useful tools
tags:
  - Circuit Breaker
  - dotNet
  - External integration
  - Polly
  - retry-logic
---
[<img class="alignright size-full wp-image-1101" alt="Polly library logo" src="http://www.lybecker.com/blog/wp-content/uploads/Polly.png" />](http://www.lybecker.com/blog/wp-content/uploads/Polly.png)If you do not know [Polly](https://github.com/michael-wolfenden/Polly "Polly source repository and documentation"), you are missing out! I did not know about it until a couple of days ago and you properly never heard about it either, as this wonderful little library only has 63 downloads on [NuGet](https://www.nuget.org/packages/Polly/ "Polly at NuGet.org") at the time of writing.

Polly is an easy to use retry and [circuit breaker pattern](http://en.wikipedia.org/wiki/Circuit_breaker_design_pattern "Circuit breaker design pattern on Wikipedia") implementation for .Net – let me show you.
Start by specifying the policy – what should happen when an exception thrown:

<pre class="brush: csharp; title: ; notranslate" title="">var policy = Policy
    .Handle&lt;SqlException(e =&gt; e.Number == 1205) // Handling deadlock victim
    .Or&lt;OtherException&gt;()
    .Retry(3, (exception, retyCount, context) =&gt;
    {
      // Log...
    });
</pre>

The above policy specifies a SqlExeption with number 1205 or OtherException should be retried three times – if it still fails log and bobble the original exception up the call stack.

<pre class="brush: csharp; title: ; notranslate" title="">var result = policy.Execute(() =&gt; FetchData(p1, p2));
</pre>

It is also possible to specify the time between retries – e.g. exponential back off:

<pre class="brush: csharp; title: ; notranslate" title="">var policy = Policy
    .Handle&lt;MyException&gt;()
    .WaitAndRetry(5, retryAttempt =&gt;
      TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)
    );
</pre>

Or the circuit breaker safeguarding against the same error occurs again and again if an external system is temporarily unavailable:

<pre class="brush: csharp; title: ; notranslate" title="">var policy = Policy
    .Handle&lt;TimeoutException&gt;()
    .CircuitBreaker(2, TimeSpan.FromMinutes(1));
</pre>

Go get it – I’m already using it 🙂
