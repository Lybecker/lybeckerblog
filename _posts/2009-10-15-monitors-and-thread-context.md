---

title: Monitors and thread context
date: 2009-10-15T20:27:54+01:00


guid: http://www.lybecker.com/blog/?p=361
permalink: /2009/10/15/monitors-and-thread-context/
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

<pre class="brush: csharp; title: ; notranslate" title="">var syncRoot = new object();

Monitor.Enter(syncRoot);

ThreadPool.QueueUserWorkItem(x =&gt; Monitor.Exit(syncRoot));

</pre>

It will throw a SynchronizationLockException with the message &#8220;Object synchronization method was called from an unsynchronized block of code.&#8221;

It is because System.Threading.Monitor requires the Enter and Exit methods must be executed on the same thread for the same synchronization object.

I did not know that :-/
