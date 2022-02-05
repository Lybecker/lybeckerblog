---
title: SQL Server build version
permalink: /blog/2010/03/17/sql-server-build-version/
jd_tweet_this:
  - 'yes'
  - 'yes'
dsq_thread_id:
  - "3798430830"
  - "3798430830"
categories:
  - SQL Server
  - Useful tools
tags:
  - SQL Server
---
![Databases](/wp-content/uploads/Databases.jpg){: .align-right}

Working with SQL Server it is often important to know which edition, version and service pack applied to the instance.

This information easily retrieve with either of these two system functions ServerProperty or @@Version:

```sql
SELECT @@VERSION

SELECT SERVERPROPERTY('ProductVersion'),
       SERVERPROPERTY('ProductLevel'),
       SERVERPROPERTY('Edition')
```

Both of the returns roughly the same information, but I tend to use the @@Version function as it easier to remember and type.

With the ServerProperty function additional information can be retrieved like MachineName, InstanceName or BuildClrVersion. [See more about the ServerProperty function on MSDN](http://msdn.microsoft.com/en-us/library/ms174396.aspx "ServerProperty function on MSDN").

From the build number alone it is possible to figure out which version of the SQL Server and Service Packs applied via the below table:

||RTM|SP1|SP2|SP3|SP4|
|--- |--- |--- |--- |--- |--- |
|SQL Server 2008 R2|10.50.1600.1|||||
|SQL Server 2008|10.00.1600.22|10.00.2531|10.00.4000|||
|SQL Server 2005|9.00.1399.06|9.00.2047|9.00.3042|9.00.4035||
|SQL Server 2000|8.00.194|8.00.384|8.00.532|8.00.760|8.00.2039|

Credit for the above table is due to [this site](http://sqlserverbuilds.blogspot.com/).

**Update** April 30th 2010: Added SQL Server 2008 R2 RTM build number

**Update** October 4th 2010: Added SQL Server 2008 SP2 build number
