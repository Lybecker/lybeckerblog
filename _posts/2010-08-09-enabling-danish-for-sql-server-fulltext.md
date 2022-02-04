---
id: 704
title: Enabling Danish for SQL Server FullText
date: 2010-08-09T19:10:19+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=704
permalink: /2010/08/09/enabling-danish-for-sql-server-fulltext/
dsq_thread_id:
  - "3456318034"
  - "3456318034"
categories:
  - Full Text Search
  - MS FullText
  - SQL Server
tags:
  - Danish
  - FullText
  - IFilter
  - SQL Server
  - Stemmer
  - Wordbreaker
---
SQL Server FullText enables you to search large amount of strings fast, and it is easy to use. It hasn’t changed much since SQL Server 2000.  
A simple getting started tutorial can be found on [Code Project](http://www.codeproject.com/KB/database/SQLServer2K8FullTextSearh.aspx "Creating Full Text Catalog and Full Text Search").

SQL Server FullText is easy to use in applications requiring string searching.

The Danish, Polish and Turkish wordbreaker and stemmer implementations for SQL Server FullText is not developed by Microsoft and therefore not enabled by default. The libraries are however part of the installation process and are therefore present on disk.

To make use of the Danish language capabilities in SQL Server 2008, register the libraries in registry and reload the FullText languages:

  1. Download & run the [DanishFulltext.reg](http://www.lybecker.com/blog/wp-content/uploads/DanishFullText.zip) file on the server. It will register wordbreaker, stemmer and default location of the thesaurus xml file.
  2. Run the exec sp\_fulltext\_service &#8216;update_languages&#8217; in a Management Studio.

Now verify that Danish is enabled with this query: SELECT name FROM sys.fulltext_languages

Note: The DanishFullText.reg assumes that SQL Server is a default instance (not a named instance). If not, modify the file by changing the MSSQL10.MSSQLSERVER to the instance name.

It is the same case with Polish and Turkish – they are not registered by default. See more in the MSDN article [How to: Load Licensed Third-Party Word Breakers](http://msdn.microsoft.com/en-us/library/ms345188.aspx "MSDN article").

List of out of the box SQL Server 2008 FullText supported languages: Arabic, Bengali (India), Brazilian, British English, Bulgarian, Catalan, Chinese (Hong Kong SAR, PRC), Chinese (Macau SAR), Chinese (Singapore), Croatian, Danish, Dutch, English, French, German, Gujarati, Hebrew, Hindi, Icelandic, Indonesian ,Italian, Japanese, Kannada, Korean, Latvian, Lithuanian, Malay &#8211; Malaysia, Malayalam, Marathi, Neutral, Norwegian (Bokmål), Polish, Portuguese, Punjabi, Romanian, Russian, Serbian (Cyrillic), Serbian (Latin), Simplified Chinese, Slovak, Slovenian, Spanish, Swedish, Tamil, Telugu, Thai, Traditional Chinese, Turkish, Ukrainian, Urdu, Vietnamese.