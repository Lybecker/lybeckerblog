---
title: The nameof operator
excerpt: Easy way of using reflection to get the name of a class, property or method
permalink: /blog/2015/01/08/the-nameof-operator/
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

```csharp
var p = new Person();

Console.WriteLine(nameof(Person));
Console.WriteLine(nameof(p));
Console.WriteLine(nameof(Person.Name));
Console.WriteLine(nameof(Person.HomeAddress));

// Output:
//   Person
//   p
//   Name
//   HomeAddress
```

This is handy when doing input validation by keeping the method parameter and the parameter name of the `ArgumentNullException` in sync.

```csharp
public Point AddPoint(Point point)
{
  if (point == null)
    throw new ArgumentNullException(nameof(point));
}
```

The `nameof` operator is useful when implementing the `INotifyPropertyChanged` interface

```csharp
public string Name
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
```

The [Chained null checks](/blog/2015/01/06/chained-null-checks/ "Chained null checks blog post by Anders Lybecker") blog post shows how to simplify triggering event in the OnPropertyChanged with the null-conditional operator.
