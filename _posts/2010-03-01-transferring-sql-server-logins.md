---

title: Transferring SQL Server logins
date: 2010-03-01T18:31:53+01:00


guid: http://www.lybecker.com/blog/?p=518
permalink: /blog/2010/03/01/transferring-sql-server-logins/
dsq_thread_id:
  - "3758113725"
  - "3758113725"
categories:
  - Security
  - SQL Server
tags:
  - Security
  - SQL Server
  - SQL Server 2008
---
<img loading="lazy" class="alignright size-full wp-image-520" title="Files" src="http://www.lybecker.com/blog/wp-content/uploads/Files.png" alt="" width="158" height="91" />I had to migrate a SQL Server 2008 instance on to new hardware.

I choose to detach the databases and attach the databases on the new SQL Server 2008 instance on the new hardware. This was easy, but the origin SQL Server instance used mixed SQL Server and Windows Authentication Mode. This also meant that I had to migrate SQL Server logins, as the logins where a mix of SQL Server logins and Active Directory domain accounts/groups.

Both the SQL Server logins and domain accounts/groups has an unique [SID](http://en.wikipedia.org/wiki/Security_Identifier "SID explained on Wikipedia") (Security Identifier), which ties the logins in the SQL Server with the users in the database.

While the domain accounts/groups resides in the Active Directory domain controllers, the SQL Server logins only resides in the SQL Server. This means, moving domain accounts/groups is easy, by just creating the same users in the new SQL Server instance, but SQL Server logins is not. I could either choose to:

  * Recreate the SQL Server logins with new users in each of the databases, as new SQL Server logins will get new SIDs and therefore not be tied to the old database users. This also requires that you know all the passwords – alternative reconfigure all the client applications using SQL Server logins 🙁
  * Transfer the SQL Server logins with SID and password 🙂

How to transfer SQL Server logins? See the KB article: [How to transfer the logins and the passwords between instances of SQL Server 2005 and SQL Server 2008](http://support.microsoft.com/default.aspx/kb/918992/ "KB article"). Easy 🙂
