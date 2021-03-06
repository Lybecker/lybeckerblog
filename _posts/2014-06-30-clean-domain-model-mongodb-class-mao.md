---
title: Clean Domain Model via MongoDB Class Map
excerpt: Using a mapping class instead of cluttering the domain model with MongoDB specific attributes
permalink: /blog/2014/06/30/clean-domain-model-mongodb-class-mao/
dsq_thread_id:
  - "3460979297"
  - "3460979297"
categories:
  - .Net
  - MongoDB
tags:
  - BSonId
  - Class Map
  - MongoDB
---
You can get rid of the dependency from the domain model to MongoDB assemblies, by utilizing [Class Map](http://docs.mongodb.org/ecosystem/tutorial/serialize-documents-with-the-csharp-driver/#creating-a-class-map "Class Map MongoDB Documentation") functionality of the [Official MongoDB C# Driver](https://www.nuget.org/packages/mongocsharpdriver "The Official MongoDB C# Driver on NuGet").

It is simple to use the property mapping, but you can also use AutoMap combined with one or more overriding mappings. That is often useful when using the MongoDB IdGenerators. However, how do you specify which to use without using attributes?

Below is a simple class with no dependencies – only dependent on the .NET Base Class Library:

```csharp
public class Person
{
  public string Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

Notice that not event an `ObjecId` is present.
To instruct MongoDB to generate a unique identifier for the Id property:

```csharp
public class Person
{
  [BsonId]
  public string Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

But now there is a dependency on the BSonId attribute. We can remove it via a Class Map:

```csharp
BsonClassMap.RegisterClassMap(cm =>
            {
                cm.AutoMap();
                cm.SetIdMember(cm.GetMemberMap(x => x.Id)
                  .SetIdGenerator(StringObjectIdGenerator.Instance));
            });
```

It is possible to use other data types as unique identifiers; just choose the correspond [IdGenerator](http://docs.mongodb.org/ecosystem/tutorial/serialize-documents-with-the-csharp-driver/#selecting-an-idgenerator-to-use-for-an-id-field-or-property "IdGenerators MongoDB Documentation").
