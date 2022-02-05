---

title: 'Scary SQL Server 2005 &#038; 2008 bug'
date: 2009-03-21T08:40:00+01:00


guid: http://www.lybecker.com/blog/?p=281
permalink: /blog/2009/03/21/scary-sql-server-2005-2008-bug/
dsq_thread_id:
  - "3604935378"
  - "3604935378"
categories:
  - SQL Server
tags:
  - SQL Server
---
Watch out when using @@IDENTITY and SCOPE_IDENTITY() in your applications. Below is a quote from a Microsoft SQL Server engineer.

> &#8230; whenever a parallel query plan is generated @@IDENTITY and SCOPE_IDENTITY() are not being updated consistently and can't be relied upon.

You can find more about the bug and workarounds at the [SQL Server bug  report](https://connect.microsoft.com/SQLServer/feedback/ViewFeedback.aspx?FeedbackID=328811 "SQL Server @@IDENTITY and SCOPE_IDENTITY() bug report").

UPDATE 28MAR2009: [Pinal Dave has a more detailed blog post about the bug with workaround](http://blog.sqlauthority.com/2009/03/24/sql-server-2008-scope_identity-bug-with-multi-processor-parallel-plan-and-solution/ "SQL SERVER - 2008 - SCOPE_IDENTITY Bug with Multi Processor Parallel Plan and Solution").
