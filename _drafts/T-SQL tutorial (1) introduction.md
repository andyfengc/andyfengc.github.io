---
layout: post
title: T-SQL Tutorial (1) - introduction
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

## WITH TIES
`WITH TIES` specifies that additional rows be returned from the base result set with the same value in the ORDER BY columns appearing as the last of the TOP n (PERCENT) rows. `TOP...WITH TIES` can be specified only in SELECT statements, and only if an ORDER BY clause is specified.

e.g. 

	CREATE TABLE MyTable(
			ID INT
	);
	
	INSERT INTO MyTable VALUES (1),(2),(3),(4),(5),(5)

with ties

	SELECT TOP 5 WITH TIES *
	FROM MyTable 
	ORDER BY ID;

RESULT - 1 2 3 4 5 5

without ties

	SELECT TOP 5 *
	FROM MyTable 
	ORDER BY ID;

RESULT - 1 2 3 4 5

## WITH
It is used to specify a temporary named result set, also named a common table expression (CTE). It is derived from a simple query and can be defined within the scope of a single SELECT, INSERT, UPDATE, DELETE statement or in a CREATE VIEW statement as part of defining SELECT statement.

e.g. 

-- Define the CTE expression name and column list.
  
	WITH Sales_CTE (SalesPersonID, SalesOrderID, SalesYear)  
	AS  
	-- Define the CTE query.  
	(  
	    SELECT SalesPersonID, SalesOrderID, YEAR(OrderDate) AS SalesYear  
	    FROM Sales.SalesOrderHeader  
	    WHERE SalesPersonID IS NOT NULL  
	)  
	-- Define the outer query referencing the CTE name.  
	SELECT SalesPersonID, COUNT(SalesOrderID) AS TotalSales, SalesYear  
	FROM Sales_CTE  
	GROUP BY SalesYear, SalesPersonID  
	ORDER BY SalesPersonID, SalesYear;  
	GO  

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
An index is an on-disk structure associated with a table or view that speeds retrieval of rows from the table or view. An index contains keys built from one or more columns in the table or view. These keys are stored in a structure (B-tree) that enables database to find the row or rows associated with the key values quickly and efficiently. Index is actually a pointer to data in base table. 

## Pros and cons ##
Index on a particular table of our database is to make it faster to search through the table and find the row or rows that we want. Index can be regarded as there is a copy of that column with the sorted data that the database will use to search. The new indexed column was sorted with pointers to the actual rows in base table.   

Index has pros and cons. The downside is that indexes make it slower to add rows or make updates to existing rows for that table. So adding indexes can increase read performance and decrease write performance. 

> when adding a new row to a table with one or more indexes, the database adds the new entry to the table, and then it has to add a new entry into each index on that table, making sure to insert the entry into the correct spot in the index to ensure that the data is properly sorted. And this performance degradation applies to creates, updates, and deletes for the table. For this reason, adding unnecessary indexes on tables should be avoided, and indexes that are no longer used should be removed.

## When need index ##
Depending on how we query for data in our application, e want to add indexes on some of these columns to improve read performance.

If our application has a feature where we search frequently, e.g. we need search for `articles`(3 columns: title, body, and published_at) by their `title`, it might be wise to put an index on the `title` column. This will create a copy of that column where all the articles' titles are sorted.

	-- Create a nonclustered index on a table or view, default is nonclustered index
	CREATE INDEX i1 ON t1 (col1);  

Indexes can also be useful for foreign key columns when dealing with associations. Let’s say the articles table also contains an `author_id` column that corresponds to the id column on the users table. If we put an index on the `author_id `column, when we query the database for all the articles by a particular author, the results can be found much faster because all articles by that author will be grouped together.

	| author_id | published_at |     title    |     body     |
	|-----------|--------------|--------------|--------------|
	|           | 2017-08-17   | title1 xxxxx |     xxxxx    |
	|     1     | 2017-08-20   | title2 xxxxx |     xxxxx    |
	|           | 2017-08-22   | title3 xxxxx |     xxxxx    |
	|-----------|--------------|--------------|--------------|
	|           | 2017-08-14   | title4 xxxxx |     xxxxx    |
	|           | 2017-08-20   | title5 xxxxx |     xxxxx    |
	|     2     | 2017-08-21   | title6 xxxxx |     xxxxx    |
	|           | 2017-08-22   | title7 xxxxx |     xxxxx    |
	|           | 2017-08-23   | title8 xxxxx |     xxxxx    |
	|-----------|--------------|--------------|--------------|
	|     3     | 2017-08-01   | title9 xxxxx |     xxxxx    |

## More
1. A table or view can contain the following types of indexes:

	- **Clustered**: clustered index is a special type of index that reorders the way records in the table are physically stored. Therefore there can be only one clustered index per table. 

			--When creating a single clustered index on a table, you chose only one column; options seen below discussed in next course
			--Can only have ONE clustered index
			
			CREATE CLUSTERED INDEX [ClusteredIndex-20150403-160607] 
			ON [dbo]. [Names] 
			([Fname] ASC, Lname ASC)

			-- drop index
			drop index [ClusteredIndex-20150403-160607] 
			ON [dbo]. [Names] 

	- **Nonclustered**: Nonclustered indexes have a structure separate from the data rows. A nonclustered index contains the nonclustered index key values and each key value entry has a pointer to the data row that contains the key value from base table.

			--Creating a non-clustered index on a table; can have many non-clustered indexes
			
			CREATE NONCLUSTERED INDEX [NonClusteredIndex-20150403-161239] 
			ON [dbo]. [Names] ([Lname] ASC)

1. Indexes are automatically created when PRIMARY KEY and UNIQUE constraints are defined on table columns. 

1. When you create a primary key, SqlServer will automatically add a clustered index to that column.

# View
SQL view can be thought of as either a virtual table or a stored query which is stored in the database as a SELECT statement. A user can use this virtual table just as he/she would the same way a table. The view itself does not hold data. Since views are 'formed' from a base table, they can be queried, updated, and dropped.

The view is essentially a dynamic SELECT query, and if any changes are made to the originating table(s),
These changes will be reflected in the SQL VIEW automatically. In normal view, we can only exploit indexed columns directly from base tables and cannot create indexes on view. 

Views are queried like tables and do not accept parameters. An alternative solution is to implement a stored function

Benefits of using a view are as follows:

- Restrict a user to specific rows in a table by filtering row data
- Restrict a user to specific columns by filtering column data
- Join columns from multiple tables so that they look like a single table.
- Hide complexity of the code
- Use as a security mechanism by giving permissions set on the view instead of the underlying tables.

e.g. 

	CREATE VIEW vwBooks
	AS
	SELECT [BooksID]
	,[BookTitle]
	,[BookAuthor]
	FROM [AdventureWorks2012].[dbo].[Books] 

`Materialized view(Indexed View)` is a database object that contains the results of a query, where the query has been executed and the results has been stored as a physical table. In a materialized view, indexes can be built on any column.

	--Create view with schemabinding.  
	IF OBJECT_ID ('Sales.vOrders', 'view') IS NOT NULL  
	DROP VIEW Sales.vOrders ;  
	GO  
	CREATE VIEW Sales.vOrders  
	WITH SCHEMABINDING
	AS  
	    SELECT SUM(UnitPrice*OrderQty*(1.00-UnitPriceDiscount)) AS Revenue,  
	        OrderDate, ProductID, COUNT_BIG(*) AS COUNT  
	    FROM Sales.SalesOrderDetail AS od, Sales.SalesOrderHeader AS o  
	    WHERE od.SalesOrderID = o.SalesOrderID  
	    GROUP BY OrderDate, ProductID;  
	GO  
	--Create an index on the view.  
	CREATE UNIQUE CLUSTERED INDEX IDX_V1   
	    ON Sales.vOrders (OrderDate, ProductID);  
	GO 

Please note

1. `SCHEMABINDING` option can be used with views and stored procedures. It is used to prevent unauthorized or inadvertent  modifications to the objects referenced by the view/stored procedure with `SCHEMABINDING`.
2. The first index created on a view must be a unique clustered index. After the unique clustered index has been created, we can create more nonclustered indexes.
3. Creating a unique clustered index on a view improves query performance because the view is stored in the database in the same way a table with a clustered index is stored.
4. we cannot create indexed view with a CTE. e.g. 

		;WITH cte AS
		(
		   SELECT *,
		         ROW_NUMBER() OVER (PARTITION BY DocumentID ORDER BY DateCreated DESC) AS rn
		   FROM DocumentStatusLogs
		)
		SELECT *
		FROM cte
		WHERE rn = 1

5. we cannot create indexed view via derived table. i.e. query inside a from statement(subquery). e.g. 

		SELECT *
		FROM [table1name]
		WHERE [From] = 
			(SELECT MAX([From]) FROM table2name
			WHERE table1name.EmployeeID = table2name.EmployeeID)

1. we cannot create indexed view via OVER clause, which includes ranking or aggregate window functions. e.g.

		SELECT TOP 1 WITH TIES *
		FROM dbo.tbl_Employee_Level_Rel
		ORDER BY ROW_NUMBER() OVER(PARTITION BY EmployeeID ORDER BY [From] desc)

# Difference between Oracle’s “materialized views” and SQL Server’s “indexed views”? 
They both persist the results of a query, but how are they different? 

1. SQL Server’s indexed views are always kept up to date. In SQL Server, if a view’s base tables are modified, then the view’s indexes are also kept up to date in the same atomic transaction.

1. Oracle provides something similar called a `materialized view`. If Oracle’s materialized views are created without the REFRESH FAST ON COMMIT option, then the materialized view is not modified when its base tables are. So that’s one major difference. While SQL Server’s indexed views are always kept current, Oracle’s materialized views can be static.

# Stored function #
User-defined function in SQL Server accepts parameters, performs an action, such as a complex calculation, and returns the result of that action as a value. The return value can either be a scalar (single) value or a table. 

1. Scalar function

e.g. create a function named “getFormattedDate”. This function accepts a datetime type value and returns a varchar value which is actually our formatted date. default schema is `dbo`

	CREATE FUNCTION getFormattedDate
	 (
	 @DateValue AS DATETIME
	 )
	RETURNS VARCHAR(MAX)
	AS
	BEGIN
		RETURN
		  DATENAME(DW, @DateValue)+ ', '+
		  DATENAME(DAY, @DateValue)+ ' '+
		  DATENAME(MONTH, @DateValue) +', '+
		  DATENAME(YEAR, @DateValue)	 
	END

Test it

	SELECT
		name,
		[dbo].[getFormattedDate](DOB)
	FROM student

![](/images/posts/20190524-sql-1.png)

1. Table-Valued function

A table-valued function is a user-defined function that returns data of a table type. The return type of a table-valued function is a table, therefore, we can use the table-valued function just like we would use a table.

e.g. creates a function that returns a list of products including product name, model year and the list price for a specific year:

	CREATE FUNCTION udfProductInYear (
	    @model_year INT
	)
	RETURNS TABLE
	AS
	RETURN
	    SELECT 
	        product_name,
	        model_year,
	        list_price
	    FROM
	        production.products
	    WHERE
	        model_year = @model_year;

Test it:

	SELECT 
	    product_name,
	    list_price
	FROM 
	    udfProductInYear(2018);

![](/images/posts/20190913-sql-1.png)

A multi-statement table-valued function(MSTVF) is a table-valued function that returns the result of multiple statements. It is very useful because we execute multiple queries within the function and aggregate results into the returned table.

To define a multi-statement table-valued function, you use a table variable as the return value. Inside the function, we execute one or more queries and insert data into this table variable. e.g. The following udfContacts() function combines staffs and customers into a single contact list:

	CREATE FUNCTION udfContacts()
	    RETURNS @contacts TABLE (
	        first_name VARCHAR(50),
	        last_name VARCHAR(50),
	        email VARCHAR(255),
	        phone VARCHAR(25),
	        contact_type VARCHAR(20)
	    )
	AS
	BEGIN
	    INSERT INTO @contacts
	    SELECT 
	        first_name, 
	        last_name, 
	        email, 
	        phone,
	        'Staff'
	    FROM
	        sales.staffs;
	 
	    INSERT INTO @contacts
	    SELECT 
	        first_name, 
	        last_name, 
	        email, 
	        phone,
	        'Customer'
	    FROM
	        sales.customers;
	    RETURN;
	END;

We typically use table-valued functions as parameterized views. In comparison with stored procedures, the table-valued functions are more flexible because we can use them wherever tables are used.

# Stored procedure
A stored procedure is a collection of SQL statements that applications use to access and manipulate data in a database.
There are several advantages to using sprocs and they are as follows:

- they can be contained within a single location in the database (Programmability folder)
- stored procedures are cached on the server thus faster to retrieve and process
- execution plans for the process are easily reviewable without having to run the application
- provides security by limiting direct access to tables
- stored procedures can have both input and output parameters and can contain statements to control the flow of the code, such as IF and WHILE statements

create A sproc

	Create Procedure spSalesTerritory  --<< Use the 'sp' prefix to designate a stored procedure. Once created, it viewable in the SP folder under programmability folder
	AS
	
	BEGIN
		SELECT [TerritoryID]
			  ,[Name]
			  ,[CountryRegionCode]
			  ,[Group]
			  ,[SalesYTD]
			  ,[SalesLastYear]
			  ,[CostYTD]
			  ,[CostLastYear]
			  ,[rowguid]
			  ,[ModifiedDate]
		  FROM [AdventureWorks2012].[Sales].[SalesTerritory]
	  END

Executing the sprocs. 

	EXECUTE spSalesTerritory 
	EXEC spSalesTerritory

Modifying the sprocs

	ALTER Procedure [dbo].[spSalesTerritory]  --<< Use the ALTER PROCEDURE  modify and existing sproc
	AS
	
	BEGIN
		SELECT [TerritoryID]
			  ,[Name]
			  ,[CountryRegionCode]
			  ,[Group]
			  ,[SalesYTD]
			  ,[SalesLastYear]
			  ,[CostYTD]
			  ,[CostLastYear]
			  --,[rowguid]     --<< two columns omitted via commented out
		   --   ,[ModifiedDate] 
		  FROM [AdventureWorks2012].[Sales].[SalesTerritory]
	END

Delete the sproc

	DROP PROCEDURE [dbo].[spSalesTerritory]
	GO

Example of sproc using parameters:  user inputs parameters to sproc to retrieve limited data

	Create Proc spUseMultipleParameter  --<< Note that i am using Proc instead of Procedure key word
	@BookAuthor varchar (20),   
	@BookTitle varchar (20)
	
	AS
	BEGIN
		SELECT [BooksID]
			  ,[BookTitle]
			  ,[BookAuthor]
			  ,[BookQuantity]
			  ,[SoldDate]
		FROM [TSQL].[dbo].[Books]
		WHERE BookAuthor = @BookAuthor and BookTitle = @BookTitle  -- Note the where clause has both the parameter(s) as 'place holder'
	END

--Execute the sproc using the sp name with parameter(s)

	spUseMultipleParameter
	'Margaret Mitchell','Gone With The Wind'
	'Charlotte Bronte','Jane Eyre'

# Trigger
A trigger is a special type of a procedure that 'fires' when an event such as INSERT, DELETE or UPDATE occurs occurs
They are regared as 'event-driven specialized procedures', and as such are stored in and managed by the RDBMS

Triggers are primarily used for referential integrity of data. When creating a trigger we attach it to the table

-- Create a simple trigger to print a statement with current date concatinated when an Isert occurs

	CREATE TRIGGER Trg_Grades_Insert --<< Trigger name
	ON Grades                        --<< attaching the table with a trigger
	AFTER INSERT                     --<< for type of event (either insert, update or delete) Will run immediately AFTER the event has occured
	AS
	BEGIN
		PRINT 'Note that an insert has been made for grades' + ' ' + Convert(varchar (20),GETDATE(), 101) --<<  PRINT keyword prints whatever is in single quotes
	END
	GO

--Creating a trigger for all the events (Insert, Delete, Update) --<< Data definition language (DML)

	CREATE TRIGGER Trg_Grades_Insert_Delete_Update --<< Trigger name
	ON Grades                        --<< attaching the table with a trigger
	AFTER INSERT,DELETE, UPDATE        --<< for type of event (insert, update or delete)  wilf fire for any type of modification to the table
	AS
	BEGIN
		PRINT 'You either had an insert, delete or an update against your table' + ' '  + Convert(varchar (20),GETDATE(), 101) --<< PRINT keyword prints whatever is in single quotes
	END
	GO

--Using a Instead Of trigger. When using this trigger, note that no data has been inserted by the insert command!!

	CREATE TRIGGER Trg_Grades_Insert_Delete_Updates --<< Trigger name
	ON Grades                                  --<< attaching the table with a trigger
	INSTEAD OF INSERT, UPDATE           --<< for type of event (insert, update or delete)  wilL fire for any type of modification to the table
	AS
	BEGIN
		RAISERROR ('You either had an insert, delete or an update against your table',16,1) --<< PRINT keyword prints whatever is in single quotes
	END
	GO

# Execution plan
1. way1: sql server management studio > query menu > Include Actual Execution Plan(ctrl + m) > run the query > got an extra tab entitled "Execution plan" appear in the results pane

	![](/images/posts/20180824-sql-3.png)

	![](/images/posts/20180824-sql-4.png)

1. way2: sql server management studio > execute sql 

		SET STATISTICS XML ON
		GO
		.....
		.....
		SET STATISTICS XML OFF
		GO
	
	![](/images/posts/20180824-sql-2.png)

1. way3: sql server management studio > tools > sql server profiler > Events Selection" > check "Show all events" > check the "Performance" > "Showplan XML" row > run the trace

	run the script > observer the sql server profiler 
	
	![](/images/posts/20180824-sql-1.png)

# Cursor
SQL cursor is a database object which is used to retrieve data from a result set one row at a time. It is used when data needs to be updated row by row.

cursor lifecycle

- Declaring Cursor: A cursor is declared by defining the SQL statement.
- Opening Cursor: A cursor is opened for storing data retrieved from the result set.
- Fetching Cursor: When a cursor is opened, rows can be fetched from the cursor one by one or in a block to do data manipulation.
- Closing Cursor: The cursor should be closed explicitly after data manipulation.
- Deallocating Cursor: cursors should be deallocated to delete cursor definition and release all the system resources associated with the cursor.

The cursors are slower because they update tables row by row.

e.g. 
	DECLARE @emp_id int ,@emp_name varchar(20),    
	    @message varchar(max);    
	  
	PRINT '-------- EMPLOYEE DETAILS --------';
	-- declare cursor
	DECLARE emp_cursor CURSOR FOR     
		SELECT emp_id,emp_name    
		FROM Employee  
		order by emp_id;    
	  
	-- open
	OPEN emp_cursor    
	  
	-- fetch
	FETCH NEXT FROM emp_cursor     
	INTO @emp_id,@emp_name    
	  
	print 'Employee_ID  Employee_Name'       
	  
	WHILE @@FETCH_STATUS = 0    
		BEGIN    
		    print '   ' + CAST(@emp_id as varchar(10)) +'           '+  
		        cast(@emp_name as varchar(20))  
		    FETCH NEXT FROM emp_cursor     
		INTO @emp_id,@emp_name    
		   
		END     
	CLOSE emp_cursor;    
	DEALLOCATE emp_cursor; 

# References #
[Create Clustered Indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-clustered-indexes?view=sql-server-2017)

[Clustered and Nonclustered Indexes Described](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-2017)

[Configure permissions on database objects](https://docs.microsoft.com/en-gb/sql/t-sql/lesson-2-configuring-permissions-on-database-objects?view=sql-server-2017)

[Benefits of SCHEMABINDING in SQL Server](https://www.mssqltips.com/sqlservertip/4673/benefits-of-schemabinding-in-sql-server/)

[Create Indexed Views](https://docs.microsoft.com/en-us/sql/relational-databases/views/create-indexed-views?view=sql-server-2017)

[How to select the first/least/max row per group in SQL](https://www.xaprb.com/blog/2006/12/07/how-to-select-the-firstleastmax-row-per-group-in-sql/)

[Improving Performance with SQL Server 2008 Indexed Views](https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008/dd171921(v=sql.100))