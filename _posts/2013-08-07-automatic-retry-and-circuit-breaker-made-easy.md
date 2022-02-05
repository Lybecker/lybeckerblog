---
title: Automatic Retry and Circuit Breaker made easy
excerpt: To prevent cascading failures of downstream services your code should implement the circuit breaker pattern
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
![Polly logo](/wp-content/uploads/Polly.png)
{: .align-right}

If you do not know [Polly](https://github.com/michael-wolfenden/Polly "Polly source repository and documentation"), you are missing out! I did not know about it until a couple of days ago and you properly never heard about it either, as this wonderful little library only has 63 downloads on [NuGet](https://www.nuget.org/packages/Polly/ "Polly at NuGet.org") at the time of writing.

Polly is an easy to use retry and [circuit breaker pattern](http://en.wikipedia.org/wiki/Circuit_breaker_design_pattern "Circuit breaker design pattern on Wikipedia") implementation for .Net – let me show you.
Start by specifying the policy – what should happen when an exception thrown:

```csharp
var policy = Policy
    .Handle<SqlException(e => e.Number == 1205) // Handling deadlock victim
    .Or<OtherException>()
    .Retry(3, (exception, retyCount, context) =>
    {
      // Log...
    });
```

The above policy specifies a `SqlExeptio`n with number 1205 or `OtherException` should be retried three times – if it still fails log and bobble the original exception up the call stack.

```csharp
var result = policy.Execute(() => FetchData(p1, p2));
```

It is also possible to specify the time between retries – e.g. exponential back off:

```csharp
var policy = Policy
    .Handle<MyException>()
    .WaitAndRetry(5, retryAttempt =>
      TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)
    );
```

Or the circuit breaker safeguarding against the same error occurs again and again if an external system is temporarily unavailable:

```csharp
var policy = Policy
    .Handle<TimeoutException>()
    .CircuitBreaker(2, TimeSpan.FromMinutes(1));
```

Go get it – I’m already using it 🙂
