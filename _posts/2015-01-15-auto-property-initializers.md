---

title: Auto-property initializers
date: 2015-01-15T09:12:15+01:00


guid: http://www.lybecker.com/blog/?p=1270
permalink: /2015/01/15/auto-property-initializers/
dsq_thread_id:
  - "3456341153"
  - "3456341153"
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
  * Auto-property initializers (this one)
  * [Initialize a Dictionary with index initializers](/blog/2015/01/19/initialize-a-dictionary-with-index-initializers/ "Initialize a Dictionary with index initializers blog post by Anders Lybecker")

At first auto-property initializers does not sound very interesting at all, but wait…

Simple things as setting a default value for a property.

<pre class="brush: csharp; title: ; notranslate" title="">public class Order
{
  public int OrderNo { get; set; } = 1;
}
</pre>

Or using the getter-only auto-property which are implicit declared `readonly` and can therefore be set in the constructor.

<pre class="brush: csharp; title: ; notranslate" title="">public class Order
{
  public Order(int orderNo)
  {
    OrderNo = orderNo;
  }

  public int OrderNo { get; }
}
</pre>

From my point of view the value of auto-properties comes to shine when used with list properties where the list has to be initialized.

<pre class="brush: csharp; title: ; notranslate" title="">public class Order
{
  public IEnumerable&lt;OrderLine&gt; Lines { get; } = new List&lt;OrderLine&gt;();
}
</pre>

I often forget to initialize a list property in the constructor and therefor get a `NullReferenceException` when accessing the list property. Now I might even be able to omit the constructor all together.
