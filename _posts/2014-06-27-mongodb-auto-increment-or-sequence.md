---
id: 1180
title: MongoDB auto-increment or sequence
date: 2014-06-27T16:17:58+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1180
permalink: /2014/06/27/mongodb-auto-increment-or-sequence/
dsq_thread_id:
  - "3456888312"
  - "3456888312"
categories:
  - .Net
  - MongoDB
tags:
  - auto increment
  - MongoDB
  - sequence
---
MongoDB is a document database that does not have auto-increment or sequence functionality build-in, like e.g. SQL Server. Auto-increment or sequence numbers are often used to create a consecutive sequence of numbers used as order or invoice identifiers.

This is simple to achieve with MongoDB via the atomic operation [$inc](http://docs.mongodb.org/manual/reference/operator/update/inc/ "MongoDB $inc operation documentation"). If you use the [Official MongoDB C# Driver](https://www.nuget.org/packages/mongocsharpdriver "Official MongoDB C# Driver on NuGet") the operation is exposed via the Update class.

To implement auto-increment or sequence functionality with MongoDB a document is required to keep the state of the sequence. The Id is the natural name of the sequence e.g. orderid and the value is the current counter value.

<pre class="brush: csharp; title: ; notranslate" title="">class Counter
{
  public string Id { get; set; }
  public int Value { get; set; }
}
</pre>

Then getting the next ordered is done by executing the below statement:

<pre class="brush: csharp; title: ; notranslate" title="">var client = new MongoClient(connectionString);
MongoServer server = client.GetServer();
MongoDatabase db = server.GetDatabase("myDatabase");
var counterCol = db.GetCollection("counters")

var result = counterCol.FindAndModify(new FindAndModifyArgs()
{
  Query = Query.EQ(d =&gt; d.Id, "orderId"),
  Update = Update.Inc(d =&gt; d.Value, 1),
  VersionReturned = FindAndModifyDocumentVersion.Modified,
  Upsert = true, //Create if the document does not exists
});
</pre>