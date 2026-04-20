---
title: Loooong running sql statement
excerpt: "Found a SQL Server process in Profiler that had been running for over 500,000 years — a likely world record."
permalink: /blog/2009/04/06/loooong-running-sql-statement/
dsq_thread_id:
  - "5901258080"
categories:
  - Code fun
  - SQL Server
tags:
  - SQL Server
---
![Long running command](/wp-content/uploads/sqlserverprofilerrenegate.jpg)

I was using SQL Server Profiler on a SQL Server 2005 Enterprise Edition looking for performance culprits, when I stumbled upon this very long running process.

I think this must be a world record. 🙂

This statement has only used 16 milliseconds of CPU but it has been running for more than 500.000 years!
