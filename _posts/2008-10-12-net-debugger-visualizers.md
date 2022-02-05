---

title: .Net debugger visualizers
date: 2008-10-12T16:35:28+01:00


guid: http://www.lybecker.com/blog/?p=138
permalink: /blog/2008/10/12/net-debugger-visualizers/
dsq_thread_id:
  - "3572109199"
  - "3572109199"
categories:
  - .Net
  - Everyday coding
  - WCF
tags:
  - dotNet
  - Visual Studio
  - WCF
---
The .Net framework 2.0 gave some great tools for debugging &#8211; they are a great help when writing code.

Some of these [features](http://msdn.microsoft.com/en-us/library/ms228992.aspx) are the [`DebuggerDisplay`](http://msdn.microsoft.com/en-us/library/x810d419.aspx) and [`DebuggerStepThrough`](http://msdn.microsoft.com/en-us/library/system.diagnostics.debuggerstepthroughattribute.aspx) attributes. I seldom use any of them, as I prefer to just overriding the `ToString` method instead of using the `DebuggerDisplay` attribute. This makes it easy to instrument the code too, as I can reuse the `ToString` method implementation for tracing.

Another feature is the ability to develop custom debugger visualizers. They enable smart developers to [build their own](http://msdn.microsoft.com/en-us/library/ms228992.aspx). I find some really useful, especially the [WCF visualizer](http://www.codeplex.com/WCFVisualizer). But there a many &#8211; check out [this list of custom .Net debugger visualizers](http://weblogs.asp.net/vardi/archive/2008/05/31/net-debugger-visualizers-list.aspx).

These small enhancements make it so easy to get an overview of the current state, instead of inspecting properties.
