---
id: 180
title: 'Easy win &#8211; index candidates on SQL Server 2005'
date: 2008-12-01T08:43:10+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=180
permalink: /2008/12/01/easy-win-index-candidates-on-sql-server-2005/
dsq_thread_id:
  - "3456317602"
  - "3456317602"
categories:
  - Everyday coding
  - SQL Server
  - Useful tools
tags:
  - SQL Server
---
Below is my real life story telling how easy it can be to find index candidates if you use your tools.

This weekend we deployed a new version of a medium/large solution to the production environment for one of our customers. Normally this is not a big issue, as we are always prepared to roll back to the previous version &#8211; the one currently in production. The deployment and configuration processes are well-prepared and highly automated to minimize faults and late-night-bug-hunting-frenzy.

After the deployment is complete we smoke test the setup to make sure that it is configured correctly and all is sound.

This weekend after the successful deployment the smoke test failed miserably. We received TimeOutExceptions all over. It was coming from the user repository service.

We decided to investigate a little before the decision of aborting the deployment of the new version and therefore rolling back to the previous version.

The steps to resolve the issue is what I want to share with you.

A quick look in the source repository showed that almost no changes to the user repository service – none that could cause TimeOutExceptions.

With the SQL Server Profiler it was easy to spot the cause of the exception.

[<img loading="lazy" class="alignnone size-full wp-image-182" title="SQL Server Profiler trace - Easy to spot culprit" src="http://www.lybecker.com/blog/wp-content/uploads/sqlserverprofilertrace.png" alt="" width="500" height="293" />](http://www.lybecker.com/blog/wp-content/uploads/sqlserverprofilertrace.png)

This simple select statement was isolated and copied into the Management Studio to view the Actual Execution Plan. The execution plan did show much, but by execution the below statement which instructs the SQL Server not to execute the Transact-SQL  statements. Instead, SQL Server returns detailed information about how the statements are going to be executed in the form of a well-defined XML document.

<pre class="brush: sql; title: ; notranslate" title="">SET SHOWPLAN_XML ON
</pre>

The SQL Server will include loads in information and one of these are suggested indexes which you will not see with the graphical execution plan in Management Studio. Look for the MissingIndex elements:

<pre class="brush: xml; title: ; notranslate" title="">&lt;missingindexes&gt;
  &lt;missingindexgroup Impact="96.6222"&gt;
    &lt;missingindex Database="&#91;DOIPEI&#93;" Schema="&#91;dbo&#93;" Table="&#91;OrganisationalUnit_Stack&#93;"&gt;
      &lt;columngroup Usage="EQUALITY"&gt;
        &lt;column Name="&#91;org_pkid&#93;" ColumnId="2" /&gt; 
      &lt;/columngroup&gt;
    &lt;/missingindex&gt;
  &lt;/missingindexgroup&gt;
&lt;/missingindexes&gt;
</pre>

By creating this single index on [DOIPEI].[dbo]. [OrganisationalUnit\_Stack]. [org\_pkid] the TimeOutExceptions disappeared, as the query no longer took minutes but milliseconds.

We were able to continue and successfully deploy the new version of the solution with only minimal disruption and a delay of only half an hour. Why the index where missing is still a mystery.

It makes all the difference in the world if you know the capabilities of your tools at hand.