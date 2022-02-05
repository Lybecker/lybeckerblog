---
title: Lucene.Net and Transactions
excerpt: Showing hos to implement two-phase-commit (2PC) transactions in .NET
permalink: /blog/2009/12/03/lucene-net-and-transactions/
dsq_thread_id:
  - "3456317833"
  - "3456317833"
categories:
  - .Net
  - Full Text Search
  - Lucene
tags:
  - dotNet
  - Lucene.Net
  - Transactions
---
This is an old post about .NET Framework, but the `System.Transaction` is still relevant for .NET 5
{: .notice--info}

![Lucene logo](/wp-content/uploads/LuceneLogo.gif){: .align-right}

Lucene.Net is an open source full text search engine library (a port from Java). It is stable and works like a charm - I’ve been using Lucene.Net for a couple of years now and implement a handful of solutions. Lucene is awesome.

If you want to try working with Lucene.Net, then the [dimecast](http://dimecasts.net/Casts/ByTag/Lucene) crew has recently made two short webcasts introducing Lucene.Net.

.NET Framework 2.0 made it simple to use transactions with the `System.Transaction`s namespace. Two of the great features are automatic elevation to distributed transactions (and utilize the Distributed Transaction Coordinator) and the other is the simplicity of creating your own transactional resource managers.

The .Net Framework defines a resource manager as a resource that can automatically enlist in a transaction managed by System.Transactions – which means that any object that implements any of the following interfaces can enlist in a transaction:

* `IEnlistmentNotification` for the [two-phase-commit protocol](http://en.wikipedia.org/wiki/Two-phase_commit_protocol)
* `IPromotableSinglePhaseNotification` for the single-phase-commit protocol (non-distributed transactions)

To implement a resource manager for the Lucene.Net IndexWriter, and therefore make it transactional, all you have to do is the following:

```csharp
public class TransactionalIndexWriter : IndexWriter, IEnlistmentNotification
{
    public TransactionalIndexWriter(Directory d, Analyzer a, bool create, MaxFieldLength mfl)
        : base(d, a, create, mfl)
    {
        EnlistTransaction();
    }

    public void EnlistTransaction()
    {
        // Enlist in transaction if ambient transaction exists
        Transaction tx = Transaction.Current;
        if (tx != null)
            tx.EnlistVolatile(this, EnlistmentOptions.None);
    }

    #region IEnlistmentNotification Members
    public void Commit(Enlistment enlistment)
    {
        base.Commit();
        enlistment.Done();
    }

    public void InDoubt(Enlistment enlistment)
    {
        // Do nothing.
        enlistment.Done();
    }

    public void Prepare(PreparingEnlistment preparingEnlistment)
    {
        base.PrepareCommit();
        preparingEnlistment.Prepared();
    }

    public void Rollback(Enlistment enlistment)
    {
        base.Rollback();
        enlistment.Done();
    }
    #endregion
}
```

You can use it like so:

```csharp
IndexWriter indexWriter = null;
TransactionScope tx = null;

try
{
    tx = new TransactionScope();
    indexWriter = new TransactionalIndexWriter(...);

    // Perform transactional work
    indexWriter.AddDocument(new Document());
    indexWriter.AddDocument(new Document());
    indexWriter.AddDocument(new Document());

    // Connect to Database, MSMQ etc. to elevate to a distributed transaction

    // Commit transaction
    tx.Complete();
}
finally
{
    if (tx != null)
        tx.Dispose();

    if (indexWriter != null)
        indexWriter.Close();
}
```

Fairly simply uh? Just remember to instantiate the `TransactionalIndexWriter` or call the public method EnlistTransaction within the scope of an ambient transaction.
You might consider implementing `IDisposable` for `TransactionalIndexWriter` so you can take advantage of the using statement.

I will leave it to the reader to implement a `TransactionalIndexReader`.
