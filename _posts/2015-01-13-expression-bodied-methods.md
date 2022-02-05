---
title: Expression-bodied methods
excerpt: .Net short hand notation for short methods
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

```csharp
public class Person
{
  public string Name { get; set; }

  public Address HomeAddress { get; set; }

  public override string ToString()
  {
    return string.Format("{Name} lives in {HomeAddress?.City ?? "City unknown"}.");
  }
}
```

The `ToString` method can be written as a lambda function.

```csharp
public override string ToString() => string.Format("{Name} lives in {HomeAddress?.City ?? "City unknown"}.");
```

And simplified with String interpolation.

```csharp
public override string ToString() => $"{Name} lives in {HomeAddress?.City ?? "City unknown"}.
```

Use expression-bodied methods anywhere…

```csharp
public Point Move(int dx, int dy) => new Point(x + dx, y + dy);
public static Complex operator +(Complex a, Complex b) => a.Add(b);
public static implicit operator string (Person p) => "{p.First} {p.Last}";
```
