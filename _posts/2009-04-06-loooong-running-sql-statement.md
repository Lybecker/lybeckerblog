---

title: Loooong running sql statement
date: 2009-04-06T11:30:34+01:00


guid: http://www.lybecker.com/blog/?p=277
permalink: /blog/2009/04/06/loooong-running-sql-statement/
dsq_thread_id:
  - "5901258080"
categories:
  - Code fun
  - SQL Server
tags:
  - SQL Server
---
<p style="text-align: center;">
  <a href="http://www.lybecker.com/blog/wp-content/uploads/sqlserverprofilerrenegate.jpg"><img loading="lazy" class="aligncenter size-large wp-image-278" title="Sql Server Profiler renegate statement" src="http://www.lybecker.com/blog/wp-content/uploads/sqlserverprofilerrenegate-1024x298.jpg" alt="Sql Server Profiler renegate statement" width="550" height="160" /></a>
</p>

I was using SQL Server Profiler on a SQL Server 2005 Enterprise Edition looking for performance culprits, when I stumbled upon this very long running process.

I think this must be a world record. 🙂

This statement has only used 16 milliseconds of CPU but it has been running for more than 500.000 years!
