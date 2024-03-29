---
title: Awesome string formatting
excerpt: Simple way of formatting .NET strings
permalink: /blog/2015/01/09/awesome-string-formatting/
dsq_thread_id:
  - "3456341076"
  - "3456341076"
categories:
  - .Net
tags:
  - .Net
  - 'C# 6'
---
The next release of C# 6 has some amazing new features. In a series of blog posts I will cover some of them.

* [Chained null checks](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker")
* [The nameof operator](/blog/2015/01/08/the-nameof-operator/ "The nameof operator blog post by Anders Lybecker")
* Awesome string formatting (this one)
* [Expression-bodied methods](/blog/2015/01/13/expression-bodied-methods/ "Expression-bodied methods blog post by Anders Lybecker")
* [Auto-property initializers](/blog/2015/01/15/auto-property-initializers/ "Auto-property initializers blog post by Anders Lybecker")
* [Initialize a Dictionary with index initializers](/blog/2015/01/19/initialize-a-dictionary-with-index-initializers/ "Initialize a Dictionary with index initializers blog post by Anders Lybecker")

Using the versatile `string.Format` required a lot of typing and keeping the numbed placeholders in sync with the method parameters.

```csharp
var numerator = 1;
var denominator = 2;

Console.WriteLine("Fraction {0}/{1}", numerator, denominator);

// Output:
//   Fraction 1/2
```

In C# 6 is it a lot easier with String interpolation:

```csharp
var numerator = 1;
var denominator = 2;

Console.WriteLine("Fraction {numerator}/{denominator}");

// Output:
//   Fraction 1/2
```

Referencing the variable, property or field directly within the string. It is even possible to access properties or use expressions.

```csharp
public class Person
{
  public string Name { get; set; }
  public Address HomeAddress { get; set; }

  public override string ToString()
  {
    return string.Format("{Name} lives in {HomeAddress.City}.");
  }
}
```

The `string.Format` is not event needed, but use the shorthand notation $.

```csharp
return $("{Name} lives in {HomeAddress.City}.";
```

This is easily combinable with an expression and the [null-conditional operator](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker") (**?.**) operator.

```csharp
return $"{Name} lives in {HomeAddress?.City ?? "City unknown"}.";
```
