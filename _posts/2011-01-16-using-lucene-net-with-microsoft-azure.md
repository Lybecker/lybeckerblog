---
title: Using Lucene.Net with Microsoft Azure
permalink: /blog/2011/01/16/using-lucene-net-with-microsoft-azure/
dsq_thread_id:
  - "3456315462"
  - "3456315462"
categories:
  - .Net
  - Lucene
  - Windows Azure
tags:
  - .Net
  - Azure
  - Blob Storage
  - CloudDrive
  - Lucene.Net
  - Windows Azure
---
This is an old post. Today I would recommend using [Azure Cognitive Search](https://azure.microsoft.com/en-us/services/search/), which is a PaaS offering which is built upon Lucene.
{: .notice--warning}

![azure_logo](/wp-content/uploads/azure_logo-150x74.png){: .align-right}
Lucene indexes are usually stored on the file system and preferably on the local file system. In Azure there are additional types of storage with different capabilities, each with distinct benefits and drawbacks. The options for storing Lucene indexes in Azure are:

* Azure CloudDrive
* Azure Blob Storage

### Azure CloudDrive

CloudDrive is the obvious solutions, as it is comparable to on premise file systems with mountable virtual hard drives (VHDs). CloudDrive is however not the optimal choice, as CloudDrive impose notable limitations. The most significant limitation is; only one web role, worker role or VM role can mount the CloudDrive at a time with read/write access. It is possible to mount multiple read-only snapshots of a CloudDrive, but you have to manage creation of new snapshots yourself depending on acceptable staleness of the Lucene indexes.

### Azure Blob Storage

The alternative Lucene index storage solution is Blob Storage. Luckily a Lucene directory (Lucene index storage) implementation for Azure Blob Storage exists in the [Azure library for Lucene.Net](http://code.msdn.microsoft.com/AzureDirectory "Azure library for Lucene.Net home on MSDN Code Gallery"). It is called AzureDirectory and allows any role to modify the index, but only one role at a time. Furthermore each Lucene segment (See Lucene Index Segments) is stored in separate blobs, therefore utilizing many blobs at the same time. This allows the implementation to cache each segment locally and retrieve the blob from Blob Storage only when new segments are created. Consequently compound file format should not be used and optimization of the Lucene index is discouraged.

#### Code sample

Getting Lucene.Net up and running is simple, and using it with Azure library for Lucene.Net requires only the Lucene directory to be changes as highlighted below in Lucene index and search example. Most of it is Azure specific configuration pluming.

```csharp
Lucene.Net.Util.Version version = Lucene.Net.Util.Version.LUCENE_29;

CloudStorageAccount.SetConfigurationSettingPublisher(
    (configName, configSetter) =>
        configSetter(RoleEnvironment
        .GetConfigurationSettingValue(configName)));

var cloudAccount = CloudStorageAccount
    .FromConfigurationSetting("LuceneBlobStorage");

var cacheDirectory = new RAMDirectory();

var indexName = "MyLuceneIndex";
var azureDirectory =
    new AzureDirectory(cloudAccount, indexName, cacheDirectory);

var analyzer = new StandardAnalyzer(version);

// Add content to the index
var indexWriter = new IndexWriter(azureDirectory, analyzer,
    IndexWriter.MaxFieldLength.UNLIMITED);
indexWriter.SetUseCompoundFile(false);

foreach (var document in CreateDocuments())
{
    indexWriter.AddDocument(document);
}

indexWriter.Commit();
indexWriter.Close();

// Search for the content
var parser = new QueryParser(version, "text", analyzer);
Query q = parser.Parse("azure");

var searcher = new IndexSearcher(azureDirectory, true);

TopDocs hits = searcher.Search(q, null, 5, Sort.RELEVANCE);

foreach (ScoreDoc match in hits.scoreDocs)
{
    Document doc = searcher.Doc(match.doc);

    var id = doc.Get("id");
    var text = doc.Get("text");
}
searcher.Close();
```

[Download the reference example](http://www.lybecker.com/blog/wp-content/uploads/AzureLuceneIndex.zip) which uses Azure SDK 1.3 and Lucene.Net 2.9 in a console application connecting either to Development Fabric or your Blob Storage account.

### Lucene Index Segments (simplified)

Segments are the essential building block in Lucene. A Lucene index consists of one or more segments, each a standalone index. Segments are immutable and created when an IndexWriter flushes. Deletes or updates to an existing segment are therefore not removed stored in the original segment, but marked as deleted, and the new documents are stored in a new segment.

Optimizing an index reduces the number of segments, by creating a new segment with all the content and deleting the old ones.

### Azure library for Lucene.Net facts

* It is licensed under Ms-PL, so you do pretty much whatever you want to do with the code.
* Based on Block Blobs (optimized for streaming) which is in tune with Lucene’s incremental indexing architecture (immutable segments) and the caching features of the AzureDirectory voids the need for random read/write of the Blob Storage.
* Caches index segments locally in any Lucene directory (e.g. RAMDirectory) and by default in the volatile Local Storage.
* Calling Optimize recreates the entire blob, because all Lucene segment combined into one segment. Consider not optimizing.
* Do not use Lucene compound files, as index changes will recreate the entire blob. Also this stores the entire index in one blob (+metadata blobs).
* Do use a [VM role size (Small, Medium, Large or ExtraLarge)](http://msdn.microsoft.com/en-us/library/ee814754.aspx "Azure Virtual Machine sizes") where the Local Resource size is larger than the Lucene index, as the Lucene segments are cached by default in Local Resource storage.

### Azure CloudDrive facts

* Only Fixed Size VHDs are supported.
* Volatile Local Resources can be used to cache VHD content
* Based on Page Blobs (optimized for random read/write).
* Stores the entire VHS in one Page Blob and is therefore restricted to the Page Blob maximum limit of 1 TByte.
* A role can mount up to 16 drives.
* A CloudDrive can only be mounted to a single VM instance at a time for read/write access.
* Snapshot CloudDrives are read-only and can be mounted as read-only drives by multiple different roles at the same time.

### Additional Azure references

* [Understanding Block Blobs and Page Blobs](http://msdn.microsoft.com/en-us/library/ee691964.aspx "Article on MSDN")
* [Using Windows Azure Page Blobs and How to Efficiently Upload and Download Page Blobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/11/using-windows-azure-page-blobs-and-how-to-efficiently-upload-and-download-page-blobs.aspx)
* [Windows Azure CloudDrive Demo](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/03/29/windows-azure-drive-demo-at-mix-2010.aspx)
