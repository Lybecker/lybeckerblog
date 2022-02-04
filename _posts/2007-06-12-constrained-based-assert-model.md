---

title: Constrained-based assert model
date: 2007-06-12T07:45:46+01:00


guid: http://www.lybecker.com/blog/2007/06/12/constrained-based-assert-model/
permalink: /2007/06/12/constrained-based-assert-model/
autometa:
  - ""
  - ""
dsq_thread_id:
  - "3456317013"
  - "3456317013"
categories:
  - .Net
  - Testing Code
  - Useful tools
tags:
  - dotNet
---
The latest version of [NUnit Framework](http://nunit.org/) (version 2.4 and forward) comes with a new constrained-based assert model, which allows you to write complex assertions relatively easy with a new syntax. The old classical syntax for assertions with static methods still works and according to the [NUnit blog](http://nunit.com/blogs/) there are no plans to deprecate the classical assertion syntax.

<pre class="brush: csharp; title: ; notranslate" title="">Assert.AreEqual(5, 5.0);

Assert.AreSame(person, person);

Assert.Greater(7, 5);

Assert.IsInstanceOfType(typeof(ArrayList), new ArrayList());

Assert.IsAssignableFrom(typeof(ICollection), new List());

StringAssert.StartsWith("Anders", "And");

CollectionAssert.AreEquivalent(col1, col2);

FileAssert.AreEqual(stream1, stream2);
</pre>

The new constrained-based assert model uses a single method on the Assert class for all assertions:

<pre class="brush: csharp; title: ; notranslate" title="">Assert.That("Anders", new EqualConstraint("Anders"));
</pre>

The second parameter is the type of assertions – here an equal constraint that works on all types of data &#8211; primitives, collections, streams etc.

NUnit comes with a number of [constraints covering most scenarios](http://nunit.com/index.php?p=constraintModel&r=2.4.1), but also allows you to extend the model by developing [custom constraint](http://nunit.com/index.php?p=customConstraints&r=2.4.1) by realizing the IConstraint interface.

<pre class="brush: csharp; title: ; notranslate" title="">public interface IConstraint
{
    bool Matches(object actual);
    void WriteMessageTo(MessageWriter writer);
    void WriteDescriptionTo(MessageWriter writer);
    void WriteActualValueTo(MessageWriter writer);
}
</pre>

If the syntax of constrained-based assert model seams a bit to complex and not very reader friendly, the NUnit team has implemented a range of syntax helper classes like so:

<pre class="brush: csharp; title: ; notranslate" title="">Assert.That("Anders", Is.EqualTo("Anders"));
Assert.That("Anders", Is.Not.EqualTo("Anja"));
Assert.That("Hello World!", Text.StartsWith("HELLO").IgnoreCase);
Assert.That("make me happy", Text.Contains("make"));

Assert.That(person, Is.SameAs(person));
Assert.That(null, Is.Null);
Assert.That(new object(), Is.Not.Null);
Assert.That("", Is.Empty);

Assert.That(new ArrayList(), Is.Empty);
Assert.That(myCol, Is.Unique);

Assert.That(7, Is.GreaterThan(5));
Assert.That(2.0d + 2.0d, Is.EqualTo(4.0d).Within(.000005d));

Assert.That(myPerson, Is.InstanceOfType(typeof(Person)));
Assert.That(new EqualConstraint(), Is.AssignableFrom(typeof(IConstraint)));

Assert.That(new int[] { 4, 5, 6 }, Is.All.GreaterThan(0));
Assert.That(new string[] { "abc", "bac", "cab" }, Has.All.Length(3));
Assert.That(new int[] { 4, 5 }, Is.SubsetOf(new int[] { 4, 5, 6 }));
</pre>

The change of syntax separates NUnit from other unit test frameworks and therefore separates from the common approach of creating unit tests.

I like the new syntax &#8211; It is intuitive and easy to comprehend. All change is not, but it is simple rules of evolution. The notion of constraints is also found in [NMock](http://nmock.org/).
