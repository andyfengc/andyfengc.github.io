---
layout: post
title: T-SQL Tutorial (3) - transaction
author: Andy Feng
---

# Introduction
A `transaction` is a unit of work that is performed against a database. Transaction groups a set of tasks into a single execution unit. Each transaction begins with a specific task and ends when all the tasks in the group successfully complete. If any of the tasks fail, the transaction fails. Therefore, a transaction has only two results: success (all tasks succeed) or failure(any task fail).

A transaction is the propagation of one or more changes to the database. For example, if you are creating a record or updating a record or deleting a record from the table, then you are performing a transaction on that table. It is important to control these transactions to ensure the data integrity and to handle database errors.

Practically, we will cluster multiple SQL operations into a group and execute all of them together as a transaction.

Transactions have the following four standard properties, known as the acronym `ACID`.

- Atomicity − ensures that all operations within the work unit are completed successfully. Otherwise, the transaction is aborted at the point of failure and all the previous operations are rolled back to their former state.
- Consistency − ensures that the database properly changes states upon a successfully committed transaction.
- Isolation − enables transactions to operate independently of and transparent to each other.
- Durability − ensures that the result or effect of a committed transaction persists in case of a system failure.

# Commands
Transactional control commands are only used with the DML Commands such as - `INSERT`, `UPDATE` and `DELETE` only. They cannot be used while creating tables or dropping them because these operations are automatically committed in the database.

COMMIT;
> The COMMIT command is the transactional command used to save changes invoked by a transaction to the database.
> The COMMIT command saves all the transactions to the database since the last COMMIT or ROLLBACK command.

e.g. 

	DELETE FROM CUSTOMERS
	WHERE AGE = 25;
	COMMIT;

ROLLBACK;
> The ROLLBACK command is the transactional command used to undo transactions that have not already been saved to the database. 
> This command can only be used to undo transactions since the last COMMIT or ROLLBACK command was issued.

e.g. 

	DELETE FROM CUSTOMERS
	WHERE AGE = 25;
	ROLLBACK;

	Then, the delete operation would not impact the table.

SAVEPOINT SAVEPOINT_NAME;
> SAVEPOINT is a point in a transaction when you can roll the transaction back to a certain point without rolling back the entire transaction.
> This command serves only in the creation of a SAVEPOINT among all the transactional statements. The ROLLBACK command is used to undo a group of transactions.

ROLLBACK TO SAVEPOINT_NAME;
> The syntax for rolling back to a SAVEPOINT

e.g.

	SAVEPOINT SP1;
	-- Savepoint created.
	DELETE FROM CUSTOMERS WHERE ID=1;
	-- 1 row deleted.
	SAVEPOINT SP2;
	-- Savepoint created.
	DELETE FROM CUSTOMERS WHERE ID=2;
	-- 1 row deleted.
	SAVEPOINT SP3;
	-- Savepoint created.
	DELETE FROM CUSTOMERS WHERE ID=3;
	-- 1 row deleted.
	
	ROLLBACK TO SP2;
	-- ROLLBACK to the SAVEPOINT SP2 and SP3

RELEASE SAVEPOINT SAVEPOINT_NAME;
> The RELEASE SAVEPOINT command is used to remove a SAVEPOINT that you have created.
> Once a SAVEPOINT has been released, you can no longer use the ROLLBACK command to undo transactions performed since the last SAVEPOINT.

SET TRANSACTION [ READ WRITE | READ ONLY ];
> The SET TRANSACTION command can be used to initiate a database transaction. 
> This command is used to specify characteristics for the transaction that follows. For example, you can specify a transaction to be read only or read write.

# Demo

BEGIN TRY
	BEGIN TRAN;
	BEGIN          
	
	--Insert SQL Here

	END
	IF @@TRANCOUNT > 0  
	COMMIT TRAN;
END TRY

BEGIN CATCH

	ROLLBACK TRAN;

END CATCH ; 

# References
[https://www.tutorialspoint.com/sql/sql-transactions.htm](https://www.tutorialspoint.com/sql/sql-transactions.htm)

[https://docs.microsoft.com/en-us/sql/t-sql/language-elements/begin-transaction-transact-sql?view=sql-server-ver15](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/begin-transaction-transact-sql?view=sql-server-ver15)

[https://docs.microsoft.com/en-us/sql/t-sql/language-elements/throw-transact-sql?view=sql-server-ver15](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/throw-transact-sql?view=sql-server-ver15)