---
id: 1254
title: Chained null checks
date: 2015-01-06T08:36:55+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1254
permalink: /2015/01/06/chained-null-checks/
dsq_thread_id:
  - "3456331174"
  - "3456331174"
categories:
  - .Net
tags:
  - .Net
  - 'C# 6'
---
The next release of C# 6 has some amazing new features. In a series of blog posts I will cover some of them.

  * Chained null checks (this one)
  * [The nameof operator](/blog/2015/01/08/the-nameof-operator/ "The nameof operator blog post by Anders Lybecker")
  * [Awesome string formatting](/blog/2015/01/09/awesome-string-formatting/ "Awesome string formatting blog post by Anders Lybecker")
  * [Expression-bodied methods](/blog/2015/01/13/expression-bodied-methods/ "Expression-bodied methods blog post by Anders Lybecker")
  * [Auto-property initializers](/blog/2015/01/15/auto-property-initializers/ "Auto-property initializers blog post by Anders Lybecker")
  * [Initialize a Dictionary with index initializers](/blog/2015/01/19/initialize-a-dictionary-with-index-initializers/ "Initialize a Dictionary with index initializers blog post by Anders Lybecker")

Null-conditional operators is one of the features in C# 6 that will save the world from a lot of boilerplate code and a bunch of `NullReferenceExceptions`. It works as chained null checks!

<pre class="brush: csharp; title: ; notranslate" title="">Console.WriteLine(person?.HomeAddress?.City ?? "City unknown");
</pre>

Note the null-conditional operator (**?.**) after `person` and `HomeAddress`, it returns null and terminates the object reference chain, if one of the references are null.

It is the same logic as the below code that you can use today.

<pre class="brush: csharp; title: ; notranslate" title="">if (person != null)
    person.HomeAddress != null)
{
  Console.WriteLine(person.HomeAddress.City);
}
else
{
  Console.WriteLine("City unknown");
}
</pre>

The null-conditional operator will also make it easier to trigger events. Today it is required to reference the event, check if it is null before raising the event like so.

<pre class="brush: csharp; title: ; notranslate" title="">protected void OnPropertyChanged(string name)
{
  PropertyChangedEventHandler handler = PropertyChanged;

  if (handler != null)
  {
    handler(this, new PropertyChangedEventArgs(name));
  }
}
</pre>

But the null-conditional operator provides a tread-safe way of checking for null before triggering the event.

<pre class="brush: csharp; title: ; notranslate" title="">PropertyChanged?.Invoke(this, args);
</pre>