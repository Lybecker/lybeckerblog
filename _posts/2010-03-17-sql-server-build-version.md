---

title: SQL Server build version
date: 2010-03-17T19:58:01+01:00


guid: http://www.lybecker.com/blog/?p=527
permalink: /blog/2010/03/17/sql-server-build-version/
jd_tweet_this:
  - 'yes'
  - 'yes'
wp_jd_clig:
  - http://cli.gs/3bEMJ
  - http://cli.gs/3bEMJ
wp_jd_target:
  - http://cli.gs/3bEMJ
  - http://cli.gs/3bEMJ
jd_wp_twitter:
  - ' Post Edited: SQL Server build version http://cli.gs/3bEMJ'
  - ' Post Edited: SQL Server build version http://cli.gs/3bEMJ'
dsq_thread_id:
  - "3798430830"
  - "3798430830"
categories:
  - SQL Server
  - Useful tools
tags:
  - SQL Server
---
<img loading="lazy" class="alignright size-full wp-image-538" title="Databases" src="http://www.lybecker.com/blog/wp-content/uploads/Databases.jpg" alt="" width="161" height="120" />
Working with SQL Server it is often important to know which edition, version and service pack applied to the instance.

This information easily retrieve with either of these two system functions ServerProperty or @@Version:

<pre class="brush: sql; title: ; notranslate" title="">SELECT @@VERSION

SELECT SERVERPROPERTY('ProductVersion'),
       SERVERPROPERTY('ProductLevel'),
       SERVERPROPERTY('Edition')
</pre>

Both of the returns roughly the same information, but I tend to use the @@Version function as it easier to remember and type.

With the ServerProperty function additional information can be retrieved like MachineName, InstanceName or BuildClrVersion. [See more about the ServerProperty function on MSDN](http://msdn.microsoft.com/en-us/library/ms174396.aspx "ServerProperty function on MSDN").

From the build number alone it is possible to figure out which version of the SQL Server and Service Packs applied via the below table:

<table style="border-collapse: collapse;" border="1" cellspacing="0" cellpadding="4" bordercolor="#cccccc">
  <tr>
    <td>
    </td>

    <td>
      RTM
    </td>

    <td>
      SP1
    </td>

    <td>
      SP2
    </td>

    <td>
      SP3
    </td>

    <td>
      SP4
    </td>
  </tr>

  <tr>
    <td>
      SQL Server 2008 R2
    </td>

    <td>
      10.50.1600.1
    </td>

    <td>
    </td>

    <td>
    </td>

    <td>
    </td>

    <td>
    </td>
  </tr>

  <tr>
    <td>
      SQL Server 2008
    </td>

    <td>
      10.00.1600.22
    </td>

    <td>
      10.00.2531
    </td>

    <td>
      10.00.4000
    </td>

    <td>
    </td>

    <td>
    </td>
  </tr>

  <tr>
    <td>
      SQL Server 2005
    </td>

    <td>
      9.00.1399.06
    </td>

    <td>
      9.00.2047
    </td>

    <td>
      9.00.3042
    </td>

    <td>
      9.00.4035
    </td>

    <td>
    </td>
  </tr>

  <tr>
    <td>
      SQL Server 2000
    </td>

    <td>
      8.00.194
    </td>

    <td>
      8.00.384
    </td>

    <td>
      8.00.532
    </td>

    <td>
      8.00.760
    </td>

    <td>
      8.00.2039
    </td>
  </tr>
</table>

Credit for the above table is due to [this site](http://sqlserverbuilds.blogspot.com/).

**Update** April 30th 2010: Added SQL Server 2008 R2 RTM build number

**Update** October 4th 2010: Added SQL Server 2008 SP2 build number
