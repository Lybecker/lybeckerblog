---
title: Monitors and thread context
permalink: /blog/2009/10/15/monitors-and-thread-context/
dsq_thread_id:
  - "3456317767"
  - "3456317767"
categories:
  - .Net
  - Code fun
tags:
  - dotNet
---
Running the below code will fail – why?

```csharp
var syncRoot = new object();

Monitor.Enter(syncRoot);

ThreadPool.QueueUserWorkItem(x => Monitor.Exit(syncRoot));
```

It will throw a `SynchronizationLockException` with the message "&#8220;"Object synchronization method was called from an unsynchronized block of code."&#8221;"

It is because `System.Threading.Monitor` requires the Enter and Exit methods must be executed on the same thread for the same synchronization object.

I did not know that :-/
