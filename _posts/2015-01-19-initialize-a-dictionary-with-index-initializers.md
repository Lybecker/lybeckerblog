---
title: Initialize a Dictionary with index initializers
excerpt: Short hand notation to initialize a Dictionary
permalink: /blog/2015/01/19/initialize-a-dictionary-with-index-initializers/
dsq_thread_id:
  - "3456312996"
  - "3456312996"
categories:
  - .Net
tags:
  - .Net
  - 'C# 6'
---
The next release of C# 6 has some amazing new features. In a series of blog posts I will cover some of them.

* [Chained null checks](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker")
* [The nameof operator](/blog/2015/01/08/the-nameof-operator/ "The nameof operator blog post by Anders Lybecker")
* [Awesome string formatting](/blog/2015/01/09/awesome-string-formatting/ "Awesome string formatting blog post by Anders Lybecker")
* [Expression-bodied methods](/blog/2015/01/13/expression-bodied-methods/ "Expression-bodied methods blog post by Anders Lybecker")
* [Auto-property initializers](/blog/2015/01/15/auto-property-initializers/ "Auto-property initializers blog post by Anders Lybecker")
* Initialize a Dictionary with index initializers (this one)

A small but still significant feature in C# 6 is index initializers. Index initializers can be sued to initialize object members, but also dictionaries. Initializing a dictionary has always be cumbersome, but not anymore.

```csharp
var numbers = new Dictionary<int, string>
{
  [7] = "seven",
  [9] = "nine",
  [13] = "thirteen"
};
```

There are other great new features in C# that I have not touched – have a look at the blog post [New features in C# 6](http://blogs.msdn.com/b/csharpfaq/archive/2014/11/20/new-features-in-c-6.aspx "New features in C# 6 on MSDN Blogs") by Mads Torgersen, Principal Program Manager, VS Managed Languages.
