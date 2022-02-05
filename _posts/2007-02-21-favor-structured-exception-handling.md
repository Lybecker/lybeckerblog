---

title: Favor structured exception handling
date: 2007-02-21T16:36:40+01:00


guid: http://www.lybecker.com/blog/2007/02/21/favor-structured-exception-handling/
permalink: /blog/2007/02/21/favor-structured-exception-handling/
dsq_thread_id:
  - "3460989877"
  - "3460989877"
categories:
  - .Net
  - Everyday coding
tags:
  - dotNet
---
I was surprised this last week, when a discussion about error handling, whether or not to use structured exceptions in .Net, surfaced at a customer where I am helping them to complete a project. I will not name the company, but it is a Danish division of a large American IT corporation with around 80.000 employees.

The discussion was between one of their architects, a couple of others and me. The company where accustomed to used return codes and the architect argued in favor of return codes. I didn’t think in this day and age that organizations still use return codes with languages that have build-in support for structured exception handling.

An exception is an event that occurs during the execution of a program, designed to handle the occurrence of some exceptional condition which changes the normal flow of execution. Exceptions travel out of band and propagate through the call stack until an exception handler catches the exception. Structured exceptions use the Try&#8230;Catch&#8230;Finally syntax.
Below is a make believe example of a simple system calling two methods:

```csharp
static void Main(string[] args)
{
  Order myOrder = new Order();
  decimal yield;

  try
  {
    yield = CalculateYield(myOrder);
    UpdateStatus(myOrder);
  }
  catch (Exception ex)
  {
  //** Exception handling **//
  }
}

public static decimal CalculateYield(Order order)
{
  // calculate yield
  return decimal.MaxValue;
}

public static void UpdateStatus(Order order)
{
  // update status
}
</pre>

If an exception is thrown in the method CalculateYield the flow is short-circuited and the method UpdateStatus is never called. With return codes the developer has to manually check if the method failed and manually propagate the return code up the stack.

Return codes imposes on method signature by requiring the return type to be a return code and potentially use a parameter as return type. This clutters the method signature and does not make the method functionality apparent. Notice the confusing method signature of CalculateYield in the following code:

```csharp
static void Main(string[] args)
{
  Order myOrder = new Order();
  decimal yield = 0;
  int returnCode = 0;

  returnCode = CalculateYield(myOrder, yield);

  if (returnCode < = 0)
  {
    returnCode = UpdateStatus(myOrder);
  }
  else
  {
    //** Error handling **//
  }

  if (returnCode > 0)
  {
    //** Error handling **//
  }
}

public static int CalculateYield(Order order, decimal yield)
{
  // calculate yield
  return -1; // return code
}

public static int UpdateStatus(Order order)
{
  // update status
  return 0; // return code
}
</pre>

Return codes are inferior to exceptions. Exceptions are instances of a class and can therefore carry detailed information – not like integer return codes! It is non-reputable that return codes are no good.

The return code advocate might bring up an issue like performance due to the fact that throwing an exception results in a stack walk to create the stack trace.

_When to throw exceptions?_

Do not throw exceptions in the normal flow of execution. If it is expected that an action might fail like a login, use the Tester-Doer Pattern (e.g. Collection.Contains) or Try-Parse Pattern (e.g. Double.TryParse).

_When to catch exceptions?_

Catch only an exception if detailed information about the exception can be added, the exception is handled or some sort of compensation action is required. If the flow of execution can not continue, rethrow the exception so an exception can be handled further up the stack, e.g. inform the user.

```csharp
catch (ArgumentException ex)
{
  // some clean up or logging
  throw ex;
}
</pre>

When rethrowing exceptions, it is important to do it the right way. By using the above pattern regarding throwing and not throwing ex, the stack walk is not performed twice, making the rethrow virtually free.

If you want to know more read the book [Framework Design Guidelines](http://www.amazon.com/Framework-Design-Guidelines-Conventions-Development/dp/0321246756/sr=8-1/qid=1172001823/ref=pd_bbs_sr_1/105-0914697-9373229?ie=UTF8&s=books). It is recommended and easily read. You can also look at the blog entry [Exception Throwing](http://blogs.msdn.com/kcwalina/archive/2005/03/16/396787.aspx) by Krzysztof Cwalina &#8211; one of the authors of the Framework Design Guidelines book. There is also a small summery at [Design Guidelines for Exceptions](http://msdn2.microsoft.com/en-us/library/ms229014%28VS.80%29.aspx) on MSDN.

On a side note: the company mentioned was as one of the first I Denmark to adopt the .Net framework &#8211; in 2000 where beta 1 was released they build their first project on the .Net framework. Go figure!
