---
title: Disable and enable all database constraints
permalink: /blog/2008/08/21/disable-and-enable-all-database-constraints/
dsq_thread_id:
  - "3456317467"
  - "3456317467"
categories:
  - SQL Server
tags:
  - SQL Server
---
We (my colleagues at Miracle and I) are currently in the process of upgrading a customer’s solutions. One of task is migrating 100 gigs of data into an existing database schema. This can easily become a tedious task and can take considerable calendar time. Therefore we decided to disable all constraints on the database, move the data and then enable them again, as we know the data integrity is sound.
We could have written a script that gets all the constraints in the database and generates a SQL script, but there is an easier way…

```sql
-- Disable all Constraints
exec sp_MSforeachtable 'ALTER TABLE ? NOCHECK CONSTRAINT ALL'

-- Enable all Constraints<
exec sp_MSforeachtable 'ALTER TABLE ? CHECK CONSTRAINT ALL'
```

That's easy! 😛

I found this on the Internet somewhere &#8211; the ´sp_MSforeachtable´ procedure is nowhere to be found in books online.
You can check if foreign keys and check constrains are disabled via the `sys.foreign_keys`  and `sys.check_constraints` views.

Another obvious triviality in our situation is disabling and enabling triggers – that is easy too:

```sql
-- Disable all Triggers
exec sp_MSforeachtable 'ALTER TABLE ? DISABLE TRIGGER ALL'

-- Enable all Triggers
exec sp_MSforeachtable 'ALTER TABLE ? ENABLE TRIGGER ALL'
```
