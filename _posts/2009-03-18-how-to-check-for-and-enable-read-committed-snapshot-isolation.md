---

title: How to check for and enable read committed snapshot isolation
date: 2009-03-18T12:51:14+01:00


guid: http://www.lybecker.com/blog/?p=264
permalink: /blog/2009/03/18/how-to-check-for-and-enable-read-committed-snapshot-isolation/
dsq_thread_id:
  - "3456315366"
  - "3456315366"
categories:
  - SQL Server
tags:
  - SQL Server
  - Transactions
---
Run the below query to see which databases that uses read committed snapshot isolation:

<pre class="brush: sql; title: ; notranslate" title="">SELECT name, is_read_committed_snapshot_on FROM sys.databases
</pre>

To enable it on a specific database run the below query:

<pre class="brush: sql; title: ; notranslate" title="">ALTER DATABASE [&lt;databasename&gt;]
SET READ_COMMITTED_SNAPSHOT ON
</pre>

As a developer I am in love with read committed snapshot isolation feature for SQL Server 2005+. Transactions can read and write simultaneously the same piece of data – no read/write contention due to row versioning. Writers will block other writers though.

Normal conflicting scenarios:

_Simultaneously write and read of the same data._

  * Read committed isolation:  The reader will wait for the writer to release the exclusive lock.
  * Read committed snapshot isolation:  The reader will not block, but receive the latest committed data (latest row version).

_Simultaneously writes of the same data._

  * Read committed isolation: The second writer will wait until the first transaction completes because of an exclusive lock.
  * Read committed snapshot isolation: Same as read committed isolation.

Read more about read committed snapshot isolation and isolation levels in the [Isolation Levels in SQL Server 2005](http://www.sql-server-performance.com/articles/dba/isolation_levels_2005_p1.aspx "Isolation Levels in SQL Server 2005 on SQLServerPerformance.com") article.

Update 02FEB2011: Amended the article due to Tom’s comments.
