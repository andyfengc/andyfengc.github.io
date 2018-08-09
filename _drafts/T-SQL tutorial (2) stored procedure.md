---
layout: post
title: T-SQL Tutorial (2) - stored procedure
author: Andy Feng
---

# Introduction #
A stored procedure(sproc) is nothing more than prepared SQL code that you save so you can reuse the code over and over again. In database server side, stored procedure will be pre-compiled and cached to improve performance. It will be faster than regular query. Different from view, sproc supports parameters.

# Create stored procedure
create a stored procedure to do this the code would look like this:

	USE AdventureWorks
	GO
	
	CREATE PROCEDURE dbo.uspGetAddress
	AS
	SELECT * FROM Person.Address
	GO

To call the procedure to return the contents from the table specified, the code would be:

	EXEC dbo.uspGetAddress
	-- or
	EXEC uspGetAddress
	--or just simply
	uspGetAddress

# Declaring variables
To declare a variable inside a stored procedure, you use the DECLARE  statement as follows:

	DECLARE variable_name datatype(size) DEFAULT default_value;

e.g.

	DECLARE total_count INT DEFAULT 0;
	SET total_count = 10;

e.g. SELECT result INTO a variable

	DECLARE @name VARCHAR(30);
	SELECT @name = city FROM cities;
	PRINT @name;

# Workflow
1. IF...ELSE Statement

		DECLARE @site_value INT;
		SET @site_value = 25;
		
		IF @site_value < 25
		   PRINT 'TechOnTheNet.com';
		ELSE
		BEGIN
		   IF @site_value < 50
		      PRINT 'CheckYourMath.com';
		   ELSE
		      PRINT 'BigActivities.com';
		END;
		
		GO

1. WHILE LOOP

		DECLARE @site_value INT;
		SET @site_value = 0;
		
		WHILE @site_value <= 10
		BEGIN
		   PRINT 'Inside WHILE LOOP on TechOnTheNet.com';
		   SET @site_value = @site_value + 1;
		END;
		
		PRINT 'Done WHILE LOOP on TechOnTheNet.com';
		GO

# Input/output parameter
## Input
One parameter

	USE AdventureWorks
	GO
	
	CREATE PROCEDURE dbo.uspGetAddress @City nvarchar(30) 
	AS 
	SELECT * 
	FROM Person.Address 
	WHERE City LIKE @City + '%' 
	GO

Invoke

	EXEC dbo.uspGetAddress @City = 'New York'

Multiple Parameters: just need to list each parameter and the data type separated by a comma as shown below.

	USE AdventureWorks
	GO
	
	CREATE PROCEDURE dbo.uspGetAddress @City nvarchar(30) = NULL, @AddressLine1 nvarchar(60) = NULL
	AS
	SELECT *
	FROM Person.Address
	WHERE City = ISNULL(@City,City)
	AND AddressLine1 LIKE '%' + ISNULL(@AddressLine1 ,AddressLine1) + '%'
	GO

execute:

	EXEC dbo.uspGetAddress @City = 'Calgary'
	--or
	EXEC dbo.uspGetAddress @City = 'Calgary', @AddressLine1 = 'A'
	--or
	EXEC dbo.uspGetAddress @AddressLine1 = 'Acardia'
-- etc...

## Output
	CREATE PROCEDURE dbo.uspGetAddressCount @City nvarchar(30), @AddressCount int OUT
	AS
	SELECT @AddressCount = count(*) 
	FROM AdventureWorks.Person.Address 
	WHERE City = @City

To call this stored procedure

	DECLARE @AddressCount int
	EXEC dbo.uspGetAddressCount @City = 'Calgary', @AddressCount = @AddressCount OUTPUT
	SELECT @AddressCount

# Try ... Catch

	CREATE PROCEDURE dbo.uspTryCatchTest
	AS
	BEGIN TRY
	    SELECT 1/0
	END TRY
	BEGIN CATCH
	    SELECT ERROR_NUMBER() AS ErrorNumber
	     ,ERROR_SEVERITY() AS ErrorSeverity
	     ,ERROR_STATE() AS ErrorState
	     ,ERROR_PROCEDURE() AS ErrorProcedure
	     ,ERROR_LINE() AS ErrorLine
	     ,ERROR_MESSAGE() AS ErrorMessage;
	END CATCH

# Demo

	CREATE PROCEDURE uspGetContact
		@LastName NVARCHAR(50) 
	AS 
	/* This is a sample stored procedure to show 
	   how comments work within a stored procedure */ 
	
	-- declare variable 
	DECLARE
		@ContactID INT 
	-- set variable value 
	SET
		@ContactID = 0 
	
	-- execute stored proc and return ContactID value 
	EXEC uspFindContact @LastName=@LastName, @ContactID=@ContactID OUTPUT 
	
	-- if ContactID does not equal 0 then return data else return error 
	IF @ContactID <> 0 
	BEGIN 
	   SELECT ContactID, FirstName, LastName 
	   FROM Person.Contact 
	   WHERE ContactID = @ContactID 
	
	   SELECT d.AddressLine1, d.City, d.PostalCode 
	   FROM HumanResources.Employee a  
	       INNER JOIN HumanResources.EmployeeAddress b ON a.EmployeeID = b.EmployeeID 
	       INNER JOIN Person.Contact c ON a.ContactID = c.ContactID 
	       INNER JOIN Person.Address d ON b.AddressID = d.AddressID 
	   WHERE c.ContactID = @ContactID
	END 
	ELSE 
	BEGIN 
	   RAISERROR ('No record found',10,1) 
	END

# References #
[Stored Procedures](https://docs.microsoft.com/en-us/sql/relational-databases/stored-procedures/stored-procedures-database-engine?view=sql-server-2017)

[SQL Server Stored Procedure Tutorial](https://www.mssqltips.com/sqlservertutorial/160/sql-server-stored-procedure-tutorial/)

[SQL Tutorial](https://www.techonthenet.com/sql/index.php)