---
title: When is DivideByZeroException thrown?
permalink: /blog/2007/01/12/when-is-dividebyzeroexception-thrown/
dsq_thread_id:
  - "3456317219"
  - "3456317219"
categories:
  - .Net
  - Code fun
tags:
  - dotNet
---
What a strange question most developers will say. DivideByZeroException
is thrown every time you divide by zero, right? No – it depends.

Let’s try &#8211; regular division with integers.

```csharp
int i1 = 4;
int i2 = 0;

try
{
Console.WriteLine(i1 / i2);

}
catch (System.DivideByZeroException)
{
Console.WriteLine("DivideByZeroException");
}
```

Hmm, I see a `DivideByZeroException` coming, and that’s
absolutely correct. So what is all the fuzz about?

Let’s try again, but this time with floating point numbers.

```csharp
double d1 = 4.0;
double d2 = 0.0;

try
{
Console.WriteLine(d1 / d2);

}
catch (System.DivideByZeroException)
{
Console.WriteLine("DivideByZeroException");
}
```

If you try to run this piece of code, no exception is
thrown. Instead INF (short for infinite) is returned. To be more precise a PositiveInfinity,
due to the decimalNumerator is positive.

I read this somewhere and had to test it. I even asked
my colleagues when DivideByZeroException is thrown and showed them these code examples.
None of them knew the right answer. So it’s not only me, whom is ignorant. 🙂
