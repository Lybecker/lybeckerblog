---
title:  "!dumpheap –stat explained… (debugging .net leaks)"
date:   2005-11-25 14:41:14 +0200
categories: blog
tags:   debugging dotnet
---
The most powerful command when debugging a managed memory leak is by far `!dumpheap`. It will show you all the objects on the managed heaps and using the different switches of `!dumpheap` you can display the output in virtually any way you want.

`!dumpheap` is a function of the sos.dll extension that comes with the framework installation (in the framework directory) and if you have the SDK installed you can find some basic help for it’s usage in `C:\Program Files\Microsoft Visual Studio .NET 2003\SDK\v1.1\Tool Developers Guide\Samples\sos`.

There are two tags of objects stored on the heap

- Objects that are rooted somewhere, i.e. something in the application has a pointer to them
- Objects that have been created or un-rooted since the last garbage collection

A good trick if you want to know where you leak and want to avoid looking through a lot of data that will be garbage collected soon is to run a stress-test, then induce a GC by calling `GC.Collect(3)`, take a memory dump, then stress it a bit more, induce a GC again and take another memory dump and compare the objects on the heap.

The `–stat` switch (statistics) shows a summary, per type of the objects on the heap

```plaintext
0:000> !dumpheap -stat
0x79c489a0          1            12 System.Runtime.Remoting.Messaging.ClientContextTerminatorSink
0x79bf9aec          1            12 System.IO.TextReader/NullTextReader
0x79be7078          1            12 System.Runtime.Remoting.Proxies.ProxyAttribute
0x79bce8e0          1            12 System.Runtime.InteropServices.ComVisibleAttribute
0x79bce7c8          1            12 System.CLSCompliantAttribute
0x79bc08e0          1            12 System.Empty
0x0618ae68          1            12 System.Web.Configuration.CustomErrorsConfigHandler
0x061887f8          1            12 System.Web.UI.WebControls.UnitConverter
0x06180848          1            12 System.Drawing.ColorConverter
0x05dbfbc4          1            12 System.Data.Res

<… cut to save space …>

0x03f1236c        625     2,820,896 System.Char[]
0x04ad88f4    102,874     2,880,472 System.Web.UI.ControlCollection
0x0469bdf0    156,650     3,133,000 System.Collections.Specialized.HybridDictionary
0x04ad91bc    164,516     3,290,320 System.Web.UI.Triplet
0x03f134a8      7,582     3,799,704 System.Collections.Hashtable/bucket[]
0x04ade5e4     47,395     4,549,920 System.Web.UI.WebControls.Label
0x061826bc     58,197     4,888,548 System.Web.UI.DataBoundLiteralControl
0x04adff44    323,119     5,169,904 System.Web.UI.StateItem
0x0618788c     63,437     6,089,952 System.Web.UI.WebControls.TableCell
0x0469c5c4    309,132     6,182,640 System.Collections.Specialized.ListDictionary/DictionaryNode
0x0011cec0        305     6,240,720      Free
0x79ba2ee4    270,831     6,499,944 System.Collections.ArrayList
0x03f16d9c        222     7,703,284 System.DateTime[]
0x04add34c    105,502     8,018,152 System.Web.UI.LiteralControl
0x0615c6f4    558,019    11,160,380 System.Data.DataRowView
0x03f15d1c      3,783    15,447,528 System.Boolean[]
0x060bcc74    570,274    22,810,960 System.Data.DataRow
0x03f15fd4        702    50,930,472 System.Decimal[]
0x03f131e8     21,013    60,573,352 System.Int32[]
0x03f1209c    508,734    75,399,184 System.Object[]
0x79b94638  5,286,303   697,441,440 System.String
Total 9,712,896 objects, Total size: 1,032,127,612
```

The 1st column shows the **method table** for the type. If you dump out the actual data for an object you will find that the first DWORD is the method table and this contains links to information about the type, such as what its member variables are, what methods it implements etc. Essentially, with the method table you can uniquely identify the type.

The 2nd column lists the number of objects of this type on the heap, so in the example above we have **5,286,303 strings** on the heap.

The 3rd column shows the total size of all the objects, so again, in the example above our 5,286,303 strings occupy a total of **~695 MB**

However, total size is a truth with modification, what is shown in the 3rd column is the total size of the structures of the objects, not including member variables.

Lets take a **System.Data.DataSet** for example

```plaintext
0x060bbd2c        221        17,680 System.Data.DataSet
```

Here we can see that 221 datasets take up a total of 17,680 bytes, which means that on average they take up 80 bytes each. That would be a very, very small dataset if the size shown was the complete dataset.

If we dump out the contents of a dataset on the heap by first running `!dumpheap –mt [methodtable]` and then picking one out in the resulting list and running `!dumpobj [address]` on it we will see something like the following.

```plaintext
0:000> !dumpobj 0x3920ed4c
Name: System.Data.DataSet
MethodTable 0x060bbd2c
EEClass 0x060d2614
Size 80(0x50) bytes
GC Generation: 2
mdToken: 0x0200003b  (c:\windows\assembly\gac\system.data\1.0.5000.0__b77a5c561934e089\system.data.dll)
FieldDesc*: 0x060bb358
        MT      Field     Offset                 Type       Attr      Value Name
0x060b252c 0x4000583      0x4                CLASS   instance 0x00000000 site
0x060b252c 0x4000584      0x8                CLASS   instance 0x00000000 events
0x060b252c 0x4000582        0                CLASS     shared   static EventDisposed
    >> Domain:Value 0x001192a0:NotInit  0x0017fc40:NotInit  0x044b7b28:0x1c357cb8 <<
0x060bbd2c 0x40003d3      0xc                CLASS   instance 0x00000000 defaultViewManager
0x060bbd2c 0x40003d4     0x10                CLASS   instance 0x3920ee28 tableCollection
0x060bbd2c 0x40003d5     0x14                CLASS   instance 0x3920ed9c relationCollection
0x060bbd2c 0x40003d6     0x18                CLASS   instance 0x00000000 extendedProperties
0x060bbd2c 0x40003d7     0x1c                CLASS   instance 0x1c357c90 dataSetName
0x060bbd2c 0x40003d8     0x20                CLASS   instance 0x182d0224 _datasetPrefix
0x060bbd2c 0x40003d9     0x24                CLASS   instance 0x182d0224 namespaceURI
0x060bbd2c 0x40003da     0x40       System.Boolean   instance 0 caseSensitive
0x060bbd2c 0x40003db     0x28                CLASS   instance 0x14309a0c culture
0x060bbd2c 0x40003dc     0x41       System.Boolean   instance 1 enforceConstraints
0x060bbd2c 0x40003dd     0x42       System.Boolean   instance 0 fInReadXml
0x060bbd2c 0x40003de     0x43       System.Boolean   instance 0 fInLoadDiffgram
0x060bbd2c 0x40003df     0x44       System.Boolean   instance 0 fTopLevelTable
0x060bbd2c 0x40003e0     0x45       System.Boolean   instance 0 fInitInProgress
0x060bbd2c 0x40003e1     0x46       System.Boolean   instance 1 fEnableCascading
0x060bbd2c 0x40003e2     0x47       System.Boolean   instance 0 fIsSchemaLoading
0x060bbd2c 0x40003e3     0x2c                CLASS   instance 0x00000000 rowDiffId
0x060bbd2c 0x40003e4     0x48       System.Boolean   instance 0 fBoundToDocument
0x060bbd2c 0x40003e5     0x30                CLASS   instance 0x00000000 onPropertyChangingDelegate
0x060bbd2c 0x40003e6     0x34                CLASS   instance 0x00000000 onMergeFailed
0x060bbd2c 0x40003e7     0x38                CLASS   instance 0x00000000 onDataRowCreated
0x060bbd2c 0x40003e8     0x3c                CLASS   instance 0x00000000 onClearFunctionCalled
0x060bbd2c 0x40003e9        0                CLASS     shared   static zeroTables
    >> Domain:Value 0x0017fc40:NotInit  0x044b7b28:0x1c357c80 <<
```

So **80 bytes** is how much is needed to store all the pointers to the member variables, but the actual data is contained in the table collection and it’s member variables.  If you need to get the actual size (including member variables for the object) you can run `!objsize [address]`.

Since the member variables are objects in themselves they will be listed separately in the !dumpheap –stat output, so the end sum: **1,032,127,612 bytes** in this case, is the actual size of all data stored on the heaps.

Some types, like String, byte[], char[] contain the actual data in the structure which means that the total size shown by `!dumpheap –stat` will be the same or very close to the total size of the objects.  Both because of this and because these are very common object types you will see them as the biggest memory hoggers most of the time when you run `!dumpheap –stat` so usually when you are looking for a memory leak it is more interesting to look at some of the other more unique objects.

In this particular case we can see a huge amount of **DataRows**, so a good starting point here is to look at why there are so many DataRows. (a lot of the strings are probably entries in the DataRows), and then continue looking at the number of DataSets etc.  If there was a large number of DataSets we would be interested in why there were so many… in this case as we can see there were 221 which means that there is a large number of DataRows per DataSet which means that the datasets are pretty big. (We could run `!objsize` on a few of them to verify)

I will talk in a later post about how to track down why/where an object is rooted (i.e. what is keeping it alive) but for now I want to concentrate on the !dumpheap output.

Another important thing to notice is the Free objects. These are not really objects but rather objects that have been marked as Free during a garbage collection but where the space is not yet compacted.   If you have a lot of free space on the heap you may have a problem with pinning (a lot is a very vague term, but if more than 30% of the managed heap is Free you should definitely look into it).  For some good explanations on this, please read [Maonis blog](https://devblogs.microsoft.com/dotnet/author/maoni/)

Some other switches I use quite a bit are `–mt` (dump the objects with a specific method table), `-type` (dump the objects that match a specific pattern), `-min` (dump any objects over a specific size) and `–fix` (dump all the objects between two heap addresses)

You can combine these in pretty much any way you want like this:

```plaintext
!dumpheap –type String –min 85000
```

which will dump out all Strings on the large object heap. (85000 bytes is the limit for when an object gets stored on the large object heap vs. on the normal .net heaps).

The result will look something like this

```plaintext
0:067> !dumpheap -type String -min 85000
Using our cache to search the heap.
Address    MT           Size     Gen
0x212d0030 0x79b94638   86,812   -1 System.String dDwtMTE0NjcyOTQ4MTt0PDtsPGk8Mz
0x222d0030 0x79b94638   86,812   -1 System.String dDwtMTE0NjcyOTQ4MTt0PDtsPGk8Mz
0x232d0030 0x79b94638   86,688   -1 System.String dDwtMTE0NjcyOTQ4MTt0PDtsPGk8Mz
Statistics:
        MT      Count     TotalSize Class Name
0x79b94638          3       260,312 System.String
Total 3 objects, Total size: 260,312
```

So we had 3 strings on the large object heap, they were 86,812, 86,812 and 86,688 bytes respectively, which means that they contain around 40,000 characters each since a character is 2 bytes.

When strings are listed with `!dumpheap` the first few characters of the strings are listed for identification and a little piece of curiosa is that strings like the above that start with dDw are Base64 encoded strings. Most of the time when you see these they are ViewState strings so in this case the process is running some pages with pretty large ViewState.

Til next time...
