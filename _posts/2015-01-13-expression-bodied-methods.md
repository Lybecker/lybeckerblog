---

title: Expression-bodied methods
date: 2015-01-13T08:54:27+01:00


guid: http://www.lybecker.com/blog/?p=1266
permalink: /blog/2015/01/13/expression-bodied-methods/
dsq_thread_id:
  - "3456341137"
  - "3456341137"
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
  * Expression-bodied methods (this one)
  * [Auto-property initializers](/blog/2015/01/15/auto-property-initializers/ "Auto-property initializers blog post by Anders Lybecker")
  * [Initialize a Dictionary with index initializers](/blog/2015/01/19/initialize-a-dictionary-with-index-initializers/ "Initialize a Dictionary with index initializers blog post by Anders Lybecker")

Expression-bodied methods make it possible for methods and properties to be used as expressions instead of statement blocks, just like lambda functions.

Let’s revisit the `Person.ToString` method in the [Awesome string formatting](/blog/2015/01/09/awesome-string-formatting/ "Awesome string formatting blog post by Anders Lybecker") blog post.

<pre class="brush: csharp; title: ; notranslate" title="">public class Person
{
  public string Name { get; set; }

  public Address HomeAddress { get; set; }

  public override string ToString()
  {
    return string.Format("{Name} lives in {HomeAddress?.City ?? "City unknown"}.");
  }
}
</pre>

The `ToString` method can be written as a lambda function.

<pre class="brush: csharp; title: Expression-bodied method with String interpolation; notranslate" title="Expression-bodied method with String interpolation">public override string ToString() =&gt; string.Format("{Name} lives in {HomeAddress?.City ?? "City unknown"}.");
</pre>

And simplified with String interpolation.

<pre class="brush: csharp; title: Expression-bodied method with String interpolation and inferred string.Format; notranslate" title="Expression-bodied method with String interpolation and inferred string.Format">public override string ToString() =&gt; $"{Name} lives in {HomeAddress?.City ?? "City unknown"}.
</pre>

Use expression-bodied methods anywhere…

<pre class="brush: csharp; title: ; notranslate" title="">public Point Move(int dx, int dy) =&gt; new Point(x + dx, y + dy);
public static Complex operator +(Complex a, Complex b) =&gt; a.Add(b);
public static implicit operator string (Person p) =&gt; "{p.First} {p.Last}";
</pre>
