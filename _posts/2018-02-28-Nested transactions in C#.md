---
layout: post
title: Nested transaction in C#
author: Andy Feng
---

## Senario ##
We have a simple table with Request <-> Log is one-to-many relationship.

![](/images/posts/20180228-transaction-1.png)

script

	create table Log (
	    [Id] [bigint] not null identity,
	    [Timestamp] [datetime] not null,
	    [EmployeeNo] [nvarchar](max) null,
	    [Comment] [nvarchar](max) NULL,
	    [Request_Id] [int] NULL,
	    primary key ([Id])
	);
	create table [Request] (
	    [Id] [int] not null identity,
	    [RequesterNo] [nvarchar](max) null,
	    [RequesterEmail] [nvarchar](max) null,
	    [ApproverNo] [nvarchar](max) null,
	    [ApproverEmail] [nvarchar](max) null,
	    [Description] [nvarchar](max) null,
	    primary key ([Id])
	);
	
	alter table Log add constraint [Request_Logs] foreign key (Request_Id) references [Request]([Id]);

## A simple transaction ##

In this transaction, we simply add a new request instance.

    var db = new DemoContext();
    using (var transaction = new TransactionScope())
    {
        try
        {            
            db.Requests.Add(new Request()
            {
                ApproverEmail = "approve@gmail.com",
                RequesterNo = "emp1",
                RequesterEmail = "emp@gmail.com",
                ApproverNo = "approve1"
            });
            db.SaveChanges();
            transaction.Complete();
        }
        catch (Exception)
        {
            //
        }
    }

## Nested transactions ##
We can add a nested transaction as below. It add a request instance and a log instance.

    var db = new QatchContext();
    using (var transaction = new TransactionScope())
    {
        try
        {
            // nested transaction
            using (var t2 = new TransactionScope())
            {
                try
                {
                    db.Logs.Add(new Log()
                    {
                        Request = null,
                        EmployeeNo = "222",
                        Timestamp = DateTime.Now,
                    });
                    db.SaveChanges();
                    t2.Complete();
                }
                catch (Exception)
                {
                    //
                }
            }

            db.Requests.Add(new Request()
            {
                ApproverEmail = "approve@gmail.com",
                RequesterNo = "emp1",
                RequesterEmail = "emp@gmail.com",
                ApproverNo = "approve1"
            });
            db.SaveChanges();
            transaction.Complete();
        }
        catch (Exception)
        {
            //
        }
    }

By default, each transaction uses TransactionScopeOption.Required option and rollback outer scope will rollback both outer scope and inner scope. 

If we use TransactionScopeOption.RequiresNew then the nested scope will begin its own transaction and complete  separately from the outer scope, so it will not roll back even if the outer scope rolls back.

	using (var t2 = new TransactionScope(TransactionScopeOption.RequiresNew))

If we use TransactionScopeOption.Suppress then the nested scope will not take part in the outer transaction and will complete non-transactionally, thus does not form part of the work that would be rolled back if the outer transaction rolls back.

As a summary:

### by default, TransactionScopeOption.Required ###

|   inner    |    outer   |                            |
|------------|------------|----------------------------|
|   commit   |  rollback  |  no changes are committed  |
|   commit   |  commit    |  all changes are committed |
|   rollback |  rollback  |  no changes are committed  |
|   rollback |  commit    |  doesn't work              |

### TransactionScopeOption.RequiresNew ###
transactions of inner and outer are separate

|   inner    |    outer   |                                  |
|------------|------------|----------------------------------|
|   commit   |  rollback  |  inner committed, outer rollback |
|   commit   |  commit    |  all changes are committed       |
|   rollback |  rollback  |  no changes are committed        |
|   rollback |  commit    |  inner rollback, outer committed |

### TransactionScopeOption.Suppress ###
inner and outer scopes are separate. The transaction with TransactionScopeOption.Suppress option isn't transaction anymore and it always commit.

## Examples ##

1. Using TransactionScopeOption.RequiresNew option. inner transaction rollback but outer transaction commit. 

	    var db = new QatchContext();
	    using (var transaction = new TransactionScope())
	    {
	        try
	        {
	            // nested transaction
	            using (var t2 = new TransactionScope(TransactionScopeOption.RequiresNew))
	            {
	                try
	                {
	                    db.Logs.Add(new Log()
	                    {
	                        Request = null,
	                        EmployeeNo = "222",
	                        Timestamp = DateTime.Now,
	                    });
	                    db.SaveChanges();
	                    throw new Exception("");
	                  t2.Complete();
	                }
	                catch (Exception)
	                {
	                    //
	                }
	            }
	
	            db.Requests.Add(new Request()
	            {
	                ApproverEmail = "approve@gmail.com",
	                RequesterNo = "emp1",
	                RequesterEmail = "emp@gmail.com",
	                ApproverNo = "approve1"
	            });
	            db.SaveChanges();
	            transaction.Complete();
	        }
	        catch (Exception)
	        {
	            //
	        }
	    }

1. Using TransactionScopeOption.RequiresNew option. inner transaction commit but outer transaction rollback

	    var db = new QatchContext();
	    using (var transaction = new TransactionScope())
	    {
	        try
	        {
	            // nested transaction
	            using (var t2 = new TransactionScope(TransactionScopeOption.RequiresNew))
	            {
	                try
	                {
	                    db.Logs.Add(new Log()
	                    {
	                        Request = null,
	                        EmployeeNo = "222",
	                        Timestamp = DateTime.Now,
	                    });
	                    db.SaveChanges();
	                  t2.Complete();
	                }
	                catch (Exception)
	                {
	                    //
	                }
	            }
	
	            db.Requests.Add(new Request()
	            {
	                ApproverEmail = "approve@gmail.com",
	                RequesterNo = "emp1",
	                RequesterEmail = "emp@gmail.com",
	                ApproverNo = "approve1"
	            });
	            db.SaveChanges();
	            throw new Exception("");
	            transaction.Complete();
	        }
	        catch (Exception)
	        {
	            //
	        }
	    }

1. Using TransactionScopeOption.Suppress option. inner transaction commit and outer transaction commit too. The reason is inner transaction is not transaction anymore

	    var db = new QatchContext();
	    using (var transaction = new TransactionScope())
	    {
	        try
	        {
	            // nested transaction
	            using (var t2 = new TransactionScope(TransactionScopeOption.Suppress))
	            {
	                try
	                {
	                    db.Logs.Add(new Log()
	                    {
	                        Request = null,
	                        EmployeeNo = "222",
	                        Timestamp = DateTime.Now,
	                    });
	                    db.SaveChanges();
                        throw new Exception("");
	                  	t2.Complete();
	                }
	                catch (Exception)
	                {
	                    //
	                }
	            }
	
	            db.Requests.Add(new Request()
	            {
	                ApproverEmail = "approve@gmail.com",
	                RequesterNo = "emp1",
	                RequesterEmail = "emp@gmail.com",
	                ApproverNo = "approve1"
	            });
	            db.SaveChanges();
	            transaction.Complete();
	        }
	        catch (Exception)
	        {
	            //
	        }
	    }

