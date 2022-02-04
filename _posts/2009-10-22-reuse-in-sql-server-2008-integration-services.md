---
id: 365
title: Reuse in SQL Server 2008 Integration Services
date: 2009-10-22T20:27:34+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=365
permalink: /2009/10/22/reuse-in-sql-server-2008-integration-services/
dsq_thread_id:
  - "3459996812"
  - "3459996812"
categories:
  - SQL Server
tags:
  - SQL Server
  - SSIS
---
Or lack of 🙁

My current project requires SSIS (SQL Server 2008 Integration Services) packages for [ETL](http://en.wikipedia.org/wiki/Extract,_transform,_load "ETL at Wikipedia") processing.

SSIS seems very capable, but lacks fundamental things that a developer like me takes for granted. I did not expect SSIS to have the ability of inheritance as it isn’t object-oriented, but I did expect functions or methods like a procedural language or set-based languages like T-SQL. Sadly the answer is no.

You can make script tasks or script component with custom T-SQL or .Net code, but logic in expressions you have to duplicate.

I goggled reuse and SSIS and found this statement in an [article about reuse in SSIS](http://www.sqlservercentral.com/articles/SQL+Server+2005+-+SSIS/ssiscodereuseandcomplexcontrolflows/2021/ "Articel  	 SSIS - Code Reuse and Complex Control Flows") at SqlServerCentral.com:

> Let&#8217;s not forget, copy&paste _is_ the first level of code reuse

In essence it’s true, but I would hope the entire Information Technology industry has move way beyond this point years ago.

[This post](http://social.msdn.microsoft.com/Forums/en-US/sqlintegrationservices/thread/ffbdcec7-977d-4f5e-ab74-dff6a238bf35 "Reusability in SSIS at the Microsoft SQL Server forum") at the Microsoft SQL Server forum confirms this horrific truth about lack of reuse in SSIS 🙁