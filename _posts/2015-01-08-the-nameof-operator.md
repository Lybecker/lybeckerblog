---

title: The nameof operator
date: 2015-01-08T08:37:44+01:00


guid: http://www.lybecker.com/blog/?p=1258
permalink: /2015/01/08/the-nameof-operator/
dsq_thread_id:
  - "3456341075"
  - "3456341075"
categories:
  - .Net
tags:
  - .Net
  - 'C# 6'
---
The next release of C# 6 has some amazing new features. In a series of blog posts I will cover some of them.

  * [Chained null checks](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker")
  * The nameof operator (this one)
  * [Awesome string formatting](/blog/2015/01/09/awesome-string-formatting/ "Awesome string formatting blog post by Anders Lybecker")
  * [Expression-bodied methods](/blog/2015/01/13/expression-bodied-methods/ "Expression-bodied methods blog post by Anders Lybecker")
  * [Auto-property initializers](/blog/2015/01/15/auto-property-initializers/ "Auto-property initializers blog post by Anders Lybecker")
  * [Initialize a Dictionary with index initializers](/blog/2015/01/19/initialize-a-dictionary-with-index-initializers/ "Initialize a Dictionary with index initializers blog post by Anders Lybecker")

The `nameof` operator takes a class, method, property, field or variable and returns the string literal.

<pre class="brush: csharp; title: ; notranslate" title="">var p = new Person();

Console.WriteLine(nameof(Person));
Console.WriteLine(nameof(p));
Console.WriteLine(nameof(Person.Name));
Console.WriteLine(nameof(Person.HomeAddress));

// Output:
//   Person
//   p
//   Name
//   HomeAddress
</pre>

This is handy when doing input validation by keeping the method parameter and the parameter name of the `ArgumentNullException` in sync.

<pre class="brush: csharp; highlight: [4]; title: ; notranslate" title="">public Point AddPoint(Point point)
{
  if (point == null)
    throw new ArgumentNullException(nameof(point));
}
</pre>

The `nameof` operator is useful when implementing the `INotifyPropertyChanged` interface

<pre class="brush: csharp; highlight: [10]; title: ; notranslate" title="">public string Name
{
  get
  {
    return _name;
  }
  set
  {
    _name = value;
    this.OnPropertyChanged(nameof(Name));
  }
}
</pre>

The [Chained null checks](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker") blog post shows how to simplify triggering event in the OnPropertyChanged with the null-conditional operator.
