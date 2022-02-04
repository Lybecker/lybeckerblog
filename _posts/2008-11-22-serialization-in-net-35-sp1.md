---

title: Serialization in .Net 3.5 SP1
date: 2008-11-22T18:22:45+01:00


guid: http://www.lybecker.com/blog/?p=165
permalink: /2008/11/22/serialization-in-net-35-sp1/
dsq_thread_id:
  - "3574601296"
  - "3574601296"
categories:
  - .Net
  - WCF
tags:
  - dotNet
  - WCF
---
In .Net 3.5 SP1 it is now possible to serialize any object that has a default constructor without decorating it with [DataContract] or [Serializable].

Developers have these choices now when serializing objects:

  * Implicit serialization – requires public default constructor and only serialize public read/write fields
  * Use the [Serializable] attribute
  * Use [DataContract] and [DataMember] attributes
  * Implement the ISerializable interface

I must say, that I’m not keen on this new feature. I prefer explicit serialization to implicit serialization. I predict that implicit serialization of entire object graphs will cause performance problems for many .Net developers in the years to come. This could also be a good thing as I am a consultant  😉

Aaron Skonnard has a more detailed blog post about the [new serialization features in .Net 3.5 SP1](http://www.pluralsight.com/community/blogs/aaron/archive/2008/05/13/50934.aspx).
