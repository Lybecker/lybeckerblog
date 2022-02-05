---

title: Strings in .Net
date: 2007-04-11T16:47:50+01:00


guid: http://www.lybecker.com/blog/2007/04/11/strings-in-net/
permalink: /blog/2007/04/11/strings-in-net/
dsq_thread_id:
  - "5294198065"
categories:
  - .Net
tags:
  - dotNet
---
Strings in .Net are [immutable](http://en.wikipedia.org/wiki/Immutable_object) reference types, but can be confusing because they sometimes have similar characteristics as value types.

Below is a code sample that shows the value type characteristics of string.

```csharp
string s1 = "Anders";
string s2 = "Anders";

Console.WriteLine("s1 and s2 are equal : {0}", s1 == s2);
</pre>

Output:
s1 and s2 are equal : True

For regular reference types this would have been an object reference compare, but the == operator is overloaded to execute the same logic as the String.Equals method.

Continuing the example by implicit cast each string to an object:

```csharp
object o1 = s1;
object o2 = s2;

Console.WriteLine("o1 and o2 are equal : {0}", o1 == o2);
</pre>

Output:
o1 and o2 are equal : True

These two strings are equal due to the CLR optimization called interning. Interning works by having an internal pool of unique literal strings. If the same literal string is assigned to multiple variables the runtime retrieves the reference to the literal string in the internal pool and assigns it to each variable.

```csharp
Console.WriteLine("s1 and s2 are same : {0}", object.ReferenceEquals(s1, s2));
Console.WriteLine("o1 and o2 are same : {0}", object.ReferenceEquals(o1, o2));
</pre>

Output:
s1 and s2 are same : True
o1 and o2 are same : True

The [Object.ReferenceEquals](http://msdn2.microsoft.com/en-us/library/system.object.referenceequals.aspx) determines whether the specified object instances are the same instance.

When comparing the strings or objects all of them are the same instance! This is again due to interning. All of the variables reference to the same object.

Let us rewrite the code and try something a little bit different:

```csharp
string s1 = "Anders";
string s2 = new StringBuilder("Anders").ToString();

object o1 = s1;
object o2 = s2;

Console.WriteLine("s1 and s2 are equal : {0}", s1 == s2);
Console.WriteLine("o1 and o2 are equal : {0}", o1 == o2);
Console.WriteLine("s1 and s2 are same : {0}", object.ReferenceEquals(s1, s2));
Console.WriteLine("o1 and o2 are same : {0}", object.ReferenceEquals(o1, o2));
</pre>

Output:
s1 and s2 are equal : True
o1 and o2 are equal : False
s1 and s2 are same : False
o1 and o2 are same : False

The above code is almost identical to the previous code except that s2 is initialized with a StringBuilder. Will this return the same result? No, because interning only occur when an assembly gets loaded or the [String.Intern](http://msdn2.microsoft.com/en-us/library/system.string.intern.aspx) method is called.

The first string compare is true because of the operator overloading discussed earlier, but all the other compares are false. Even the o1 and o2 compare, because the runtime is unaware of the object types at the time of the comparison and can therefore not use the overload operator.

If you want to know more about [strings](http://www.yoda.arachsys.com/csharp/strings.html), [interning](https://blogs.msdn.com/cbrumme/archive/2003/04/22/51371.aspx) or [internationalization issues with strings](http://msdn2.microsoft.com/en-us/library/ms973919.aspx) – read on.
