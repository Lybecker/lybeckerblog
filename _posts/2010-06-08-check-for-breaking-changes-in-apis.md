---
title: Check for breaking changes in APIs
excerpt: Automating checking for changes in API's between versions
permalink: /blog/2010/06/08/check-for-breaking-changes-in-apis/
dsq_thread_id:
  - "3457703745"
  - "3457703745"
categories:
  - .Net Framework
  - Full Text Search
  - Lucene
  - Useful tools
tags:
  - Apache Lucene
  - API
  - Breaking Changes
  - Lucene
---
This is an old post, but [ApiChange](https://github.com/endjin/Endjin.ApiChange) still exists. but now on GitHub.
{: .notice--info}

Have you ever had the need to compare interfaces of two versions of the same framework?

If you have, then [ApiChange](http://apichange.codeplex.com/ "ApiChange on CodePlex") is a tool for you. It’s open source, powerful and easy to use 🙂

I gave it a spin comparing current trunk version 2.9.2 of Lucene.Net with the latest official release version 2.4.0.

I downloaded ApiChange and ran the following command in a command prompt:

```cmd
ApiChange.exe -Diff -old C:Lucene.Net_2_4_0Lucene.Net.dll -new C:trunkLucene.Net.dll
```

The output [lists all the differences](/wp-content/uploads/BreakingChangesInLucene.txt), but here is a summary:

* 23 public types where removed
* 96 public types where added
* 158 public types where changed

Cool little tool with other features such as:

* Diff public types for breaking changes.
* Who uses a method?
* Who uses a type?
* Who uses implements an interface?
* Who references me?
* What format has the binary (32/64, Managed C++, Pure IL, Unmanaged)?
* Search for all event subscribers and unsubscribers.

It’s based on [Mono Cecil](http://www.mono-project.com/Cecil "Mono Cecil's website") – a free IL parser, and not reflection as I initial thought. [Go check it out…](http://geekswithblogs.net/akraus1/archive/2010/06/03/140207.aspx "ApiChange author blog post ApiChange Is Released!")
