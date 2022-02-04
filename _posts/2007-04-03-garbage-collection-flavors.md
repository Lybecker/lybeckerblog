---
id: 11
title: Garbage Collection Flavors
date: 2007-04-03T06:54:43+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/2007/04/03/garbage-collection-flavors/
permalink: /2007/04/03/garbage-collection-flavors/
dsq_thread_id:
  - "3478642965"
  - "3478642965"
categories:
  - .Net
  - Everyday coding
tags:
  - dotNet
---
Why should you care about Garbage Collection? Well, it may very well improve performance for your applications.

The .Net framework garbage collector can operate in different modes depending on platform and requirements. There are three modes available for .Net framework version 2.0 and 3.0. Each of these modes is tailored for specific situations.

**Concurrent Workstation Garbage Collection**

This mode is optimized for interactivity by frequent short garbage collects. Each collect is split up into smaller pieces and are interleaved with the managed applications threads. This improves responsiveness at the cost of CPU cycles. This is ideal for interactive desktop applications where freezing application is an annoyance for the users and ideal CPU time is abundant when waiting for user input. Concurrent workstation mode improves the usability with [perceived performance](http://en.wikipedia.org/wiki/Perceived_performance).

Note that interactive GC only applies for Gen2 (full heap) collects because Gen0 and Gen1 collects are in nature very fast.

**Non-concurrent Workstation Garbage Collection**

Non-concurrent workstation mode works by suspending managed application threads when a GC is initiated. It provides better throughput but might appear as application hiccups where everything freezes for the users.

**Server Garbage Collection**

In server mode a managed heap and a dedicated garbage collector thread is created for each CPU. This means the each CPU allocates memory in its own heap therefore results in lock-free allocation. When a collect is initiated all the managed application threads are suspended and all the GC threads collect in parallel.  
Another thing to note is that the size of the managed heap segments is larger in server mode than workstation mode. A segment is the unit of which the memory is allocated on the managed heap.

It is possible to choose the type of GC for a managed application in the configuration file. Under the <Runtime> element add one of the below three settings and depending on the number of CPU, the garbage collector will run in the configured mode.<img loading="lazy" class="aligncenter size-full wp-image-302" title="Garbage Collection type settings" src="http://www.lybecker.com/blog/wp-content/uploads/gctypes.png" alt="Garbage Collection type settings" width="503" height="154" />

Running a standalone managed application the GC mode is by default concurrent workstation. Managed application hosts like ASP.Net and SQLCLR run with Server GC by default.

If you want to know more about how the GC works, read the blog entries &#8220;Using GC Efficiently&#8221;  
by [Maoni](http://blogs.msdn.com/maoni/).