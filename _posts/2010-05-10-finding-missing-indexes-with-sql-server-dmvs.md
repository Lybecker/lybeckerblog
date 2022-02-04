---

title: Finding Missing Indexes with SQL Server DMVs
date: 2010-05-10T22:06:43+01:00


guid: http://www.lybecker.com/blog/?p=637
permalink: /2010/05/10/finding-missing-indexes-with-sql-server-dmvs/
dsq_thread_id:
  - "3540817618"
  - "3540817618"
categories:
  - SQL Server
  - Useful tools
tags:
  - DMVs
  - Perfornance
  - SQL Server 2005
  - SQL Server 2008
---
Finding Missing Indexes with DMVsSome time ago I wrote written about [easy index wins for SQL Server 2005](http://www.lybecker.com/blog/2008/12/01/easy-win-index-candidates-on-sql-server-2005/ "Anders Lybeckers Weblog - Easy win – index candidates on SQL Server 2005").

SQL server maintains statistics about indexes you should consider creating. This time I’ll show you a DMV (Dynamic Management View) that lists index candidates. This method works for SQL Server 2005 SP2 and later versions.

The query is based on three DMVs and returns index candidates where the calculated improvement is more than 10%:

<pre class="brush: sql; title: ; notranslate" title="">SELECT
  migs.avg_total_user_cost * (migs.avg_user_impact / 100.0) * (migs.user_seeks + migs.user_scans) AS improvement_measure_pct,
  QUOTENAME(db_name(mid.database_id)) AS [database],
  QUOTENAME(OBJECT_SCHEMA_NAME(mid.object_id, mid.database_id)) AS [schema],
  QUOTENAME(OBJECT_NAME(mid.object_id, mid.database_id)) AS [table],
  'CREATE INDEX [missing_index_' + CONVERT(varchar(64), NEWID()) + ']'
  + ' ON ' + mid.statement
  + ' (' + ISNULL (mid.equality_columns, '')
  + CASE
      WHEN mid.equality_columns IS NOT NULL
	    AND mid.inequality_columns IS NOT NULL THEN ','
      ELSE ''
    END
  + ISNULL(mid.inequality_columns, '')
  + ')'
  + ISNULL(' INCLUDE (' + mid.included_columns + ')', '')
	  AS create_index_statement,
  migs.*,
  mid.database_id,
  mid.[object_id]
FROM sys.dm_db_missing_index_groups mig
  INNER JOIN sys.dm_db_missing_index_group_stats migs
	ON migs.group_handle = mig.index_group_handle
  INNER JOIN sys.dm_db_missing_index_details mid
	ON mig.index_handle = mid.index_handle
WHERE
	migs.avg_total_user_cost * (migs.avg_user_impact / 100.0) *
		(migs.user_seeks + migs.user_scans) &gt; 10
ORDER BY
	migs.avg_total_user_cost * migs.avg_user_impact *
		(migs.user_seeks + migs.user_scans) DESC
</pre>

It is important to note, that these are index candidates are only _candidates_ and the improvements are based on _estimates_. The estimated improvement does not take extra disk space requirements and the maintenance of the indexes during updates, inserts and deletes. Furthermore it does not make recommendation about usage of clustered or non-clustered indexes.

This blog post is inspired by Bart Duncan&#8217;s [Are you using SQL&#8217;s missing index DMVs](http://blogs.msdn.com/bartd/archive/2007/07/19/are-you-using-sql-s-missing-index-dmvs.aspx "Bart Duncan's SQL WebLog")?
