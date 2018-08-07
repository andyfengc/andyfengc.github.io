---
layout: post
title: T-SQL Tutorial
author: Andy Feng
---

# Introduction #
T-SQL (Transact-SQL) is an extension of SQL language. It is designed for MS SQL Server database.

download and restore archieve of AdventureWorks Sample Databases for the SQL Server 2012 at [https://archive.codeplex.com/?p=sql2012kitdb](https://archive.codeplex.com/?p=sql2012kitdb)

	```
	CREATE DATABASE AdventureWorks2012_Data
	ON (FILENAME = N'c:\server\mssql\AdventureWorks2012_Data.mdf')
	FOR ATTACH_REBUILD_LOG 
	Go
	```

if report access denied for `AdventureWorks2012_Data.mdf`, right click > mdf > security > mssqlserver > read/write permission

# Query
## Select
--using the TOP clause

	SELECT Top 5 DepartmentID,Name,GroupName
	FROM HumanResources.Department

--using the TOP with PERCENT clause
	
	SELECT TOP 5 PERCENT DepartmentID,Name,GroupName
	FROM HumanResources.Department

--Name of product NOT containing the pattern 'end'

	SELECT * FROM [AdventureWorks2012].[Production].[Product]
	WHERE Name NOT LIKE '%end%';
 
--selects all products with a name starting with "a", "b", or "m":

	SELECT * FROM [AdventureWorks2012].[Production].[Product]
	WHERE Name LIKE '[abm]%'; 

--selects all products starting with any character, followed by 'lat'

	SELECT * FROM [AdventureWorks2012].[Production].[Product]
	WHERE Name LIKE '_lat%';

--Backup table.  Use the backup table for testing.

	Select * Into [AdventureWorks2012].[Sales].[SalesOrderDetail_032415] 
	From [AdventureWorks2012].[Sales].[SalesOrderDetail]
	--(121317 row(s) affected)

--Using Between for text range values

	SELECT [ProductID]
	      ,[Name]
	      ,[ProductNumber]
	      ,[StandardCost]
	      ,[ListPrice]
		  ,SellStartDate  
	FROM [AdventureWorks2012].[Production].[Product]
	WHERE Name BETWEEN 'B' AND 'L'
	ORDER BY NAME 

# DML
## truncate
/*
With the delete command you have the option to delete all rows at once, or when using the where clause, delete specified rows 
This action does not free the space containing the table
This TRUNCATE command is used to delete all the rows from the table and free the space containing the table 
*/

--Truncate example:

	Truncate Table [AdventureWorks2012].[Sales].[SalesOrderDetail_032315]
	-- deletes all the data at once.  Cannot filter using the where clause

# temp table
A Temp (Temporary) table is a 'temporary' table that is stored in the System `Tempdb` database with a prefix of # or ##. A local temp table uses a # prefix, while a global uses two ##. We can create indexes/constraints on the local temporary tables.

Local temp tables are only available to the user who created the connection, 
and once the connection terminates, the temp table is automatically deleted. On the other hand, a global temp table once created is available to any user by any connection 
It can only be deleted when all connections that have referenced to them have closed.

When disconnecting from the server or restrating the services, all temp tables will be dropped. Note when creating Temporary tables in a the tempdb, this causes overhead and can causes performance issues 

-- Create a local temp table with a single # sign

	CREATE TABLE #Temp1
	(FName Varchar (20), 
	LName Varchar (20))

![](/images/posts/20180806-tsql-1.png)

-- Create a local temp table using a Select Into example to insert data from another table

	Select 
	[Name],
	[GroupName] 
	Into #TempDept --<< Newly created local temp table using the Select Into command
	From
	[AdventureWorks2012].[HumanResources].[Department]
	Where GroupName Like 'm%'

--Create a Global Temp Table
	
	CREATE TABLE ##GlobalTempTable(
	ID int,
	FName varchar(50), 
	Lname varchar(50))

## Constraints
Types of Constraints

- NOT NULL – Specifies that a column cannot be blank
- DEFAULT – Uses a default value, when no other values is entered in the column
- UNIQUE – Provides a unique value for each row
- PRIMARY KEY – Provides an unique identity for each record in a table 
- FOREIGN KEY – Provides the referential integrity of the data in one table to match values in another table
- CHECK - Ensures that the data entered in the column meets the condition set for that column

--The UNIQUE constraint ensures that all values in a column are distinct.

	CREATE TABLE Customer
	(CustID int UNIQUE,-- If a value is entered that violates this rule, the query will error out and terminate
	LName varchar (30),
	FName varchar(30));

--Using the ALTER TABLE command after the table has been created
 
	ALTER TABLE Customer
	ADD CHECK ( CustID >0)
 
	INSERT INTO Customer VALUES (-5, 'Shaffer', 'Georgina') -- This insert will fail because it violates the CHECK constraint that CustID must be a positive value.

-- The default constraint provides a 'set value' to be inserted into a column, when no other value is provided when using the INSERT INTO statement.
 
	CREATE TABLE Student
	(StudentID int,
	LName varchar (30),
	FName varchar (30),
	Score int DEFAULT 80);-- states, if no value is added, then add 80 by default
 
	INSERT INTO Student (StudentID, LName, FName) 
	VALUES (20, 'Smith’, ‘Tom');-- No score is added, thus a default of 80 should replace the value

## Collection
USE OF UNION operator combines the result set from two or more tables.The UNION operator selects only distinct values by default

	SELECT [BuyFname],[BuyLname]
	FROM [dbo].[Buyer]
	UNION
	SELECT [SuppFname],[SuppLname]
	FROM [dbo].[Supplier]

USE OF UNION with ALL. To allow duplicate values, use the ALL keyword with UNION

	SELECT [BuyFname],[BuyLname]
	FROM [dbo].[Buyer]
	UNION
	ALL --<< Using ALL Keyword to view duplicate records between the tables
	SELECT [SuppFname],[SuppLname]
	FROM [dbo].[Supplier]

USE OF EXCEPT. Exclude data from two or more tables that exist in both tables

	SELECT [BuyFname],[BuyLname]
	FROM [dbo].[Buyer]
	EXCEPT
	SELECT [SuppFname],[SuppLname]
	FROM [dbo].[Supplier]

USE OF INTERSECT. Command includes data from two or more tables that exist in both tables

	SELECT [BuyFname],[BuyLname]
	FROM [dbo].[Buyer]
	INTERSECT
	SELECT [SuppFname],[SuppLname]
	FROM [dbo].[Supplier]

## Alter
USE ALTER TO ADD COLUMN 

	ALTER TABLE Employee
	ADD DateOfBirth date

USE ALTER CHANGE DATA TYPE TO NOT NULL VALUE

	ALTER TABLE Employee
	ALTER COLUMN DateOfBirth varchar(15) NOT NULL

USE ALTER DROP COLUMN

	ALTER TABLE Employee
	DROP COLUMN DateOfBirth

USE ALTER TO ADD CONSTRAINT
	
	ALTER TABLE Employee
	ADD CONSTRAINT ck_Employee_AgeCHECK (Age>1 and Age<130);--<< cannot insert value less than 1 or greater than 130

USE ALTER TO DROP CONSTRAINT

	ALTER TABLE Employee
	DROP CONSTRAINT ck_Employee_Age;

# Functions
## ISNULL()
ISNULL() is to replace a NULL value with another value.

	SELECT [AddressID]
	, [AddressLine1]
	, ISNULL (AddressLine2,'UNKNOWN') AS ISNULLVALUE
	, [City]
	, [StateProvinceID]
	, [PostalCode]
	FROM [AdventureWorks2012]. [Person]. [Address]
	Order by AddressLine2

## COALESCE()
COALESCE() bypasses ALL the NULL values and returns the FIRST NON-NULL value

	SELECT
	Top 100 [BusinessEntityID]
	, COALESCE
	([Title]
	, [FirstName]
	, [MiddleName]
	, [LastName]
	, [Suffix]) AS FIND_FIRST_NON_NULL_VALUE
	FROM [AdventureWorks2012].[Person].[Person]

equivalent to

	SELECT
	Top 100 [BusinessEntityID],
	CASE
	WHEN (Title IS NOT NULL)      THEN TITLE
	WHEN (FirstName IS NOT NULL) THEN FirstName
	WHEN (MiddleName IS NOT NULL) THEN MiddleName
	WHEN (LastName IS NOT NULL)   THEN LastName
	ELSE NULL
	END AS  FIND_FIRST_NON_NULL_VALUE
	FROM [AdventureWorks2012].[Person].[Person]

![](/images/posts/20180806-tsql-2.png)


# Index

# View

# Stored procedure

# Trigger


# References #

