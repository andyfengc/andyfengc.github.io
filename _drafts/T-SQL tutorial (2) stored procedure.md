---
layout: post
title: T-SQL Tutorial (2) - stored procedure
author: Andy Feng
---

# Introduction #
A stored procedure(sproc) is prepared SQL code that you save so you can reuse the code over and over again. In database server side, stored procedure will be pre-compiled and cached to improve performance. It will be faster than regular query. Different from view, sproc supports parameters.

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
To declare a variable inside a stored procedure, you use the DECLARE statement as follows:

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

# Input parameter
One parameter

	USE AdventureWorks
	GO
	
	CREATE PROCEDURE dbo.uspGetAddress 
		@City nvarchar(30) 
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
	
	CREATE PROCEDURE dbo.uspGetAddress
		@City nvarchar(30) = NULL, 
		@AddressLine1 nvarchar(60) = NULL
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

# Return data
There are three ways of returning data from a procedure to a calling program: result sets, output parameters, and return codes. 

## Return data Using result sets
If we include a SELECT statement in the body of a stored procedure (but not a SELECT ... INTO or INSERT ... SELECT), the rows specified by the SELECT statement will be sent directly to the client. 

For large result sets the stored procedure execution will not continue to the next statement until the result set has been completely sent to the client. For small result sets the results will be spooled for return to the client and execution will continue. If multiple such SELECT statements are run during the exeuction of the stored proceudre, multiple result sets will be sent to the client. For ADO.NET, the resultset will be included in multiple tables in resultset.

e.g. Here is a stored procedure that returns the LastName and SalesYTD values for all SalesPerson rows that also appear in the `vEmployee` view.

	IF OBJECT_ID('Sales.uspGetEmployeeSalesYTD', 'P') IS NOT NULL  
	   DROP PROCEDURE Sales.uspGetEmployeeSalesYTD;  
	GO  
	CREATE PROCEDURE Sales.uspGetEmployeeSalesYTD  
	AS    
	
	   SET NOCOUNT ON;  
	   SELECT LastName, SalesYTD  
	   FROM Sales.SalesPerson AS sp  
	   JOIN HumanResources.vEmployee AS e ON e.BusinessEntityID = sp.BusinessEntityID  
	
	RETURN  
	GO

## Return data using Output parameter
If we specify the `OUTPUT` keyword for a parameter in the procedure definition, the procedure can return the current value of the parameter to the calling program when the procedure exits. (define sp) 

Also, to save the value of the parameter in a variable that can be used in the calling program, the calling program must use the OUTPUT keyword when executing the procedure. (caller)

e.g. define `OUTPUT` parameter in sp

	CREATE PROCEDURE dbo.uspGetAddressCount
		@City nvarchar(30), 
		@AddressCount int OUT
	AS
	SELECT @AddressCount = count(*) 
	FROM AdventureWorks.Person.Address 
	WHERE City = @City

In the caller (calling program), we call the stored procedure like this

	DECLARE @AddressCount int
	EXEC dbo.uspGetAddressCount @City = 'Calgary', @AddressCount = @AddressCount OUTPUT
	SELECT @AddressCount

Input values can also be specified for OUTPUT parameters when the procedure is executed. This allows the procedure to receive a value from the calling program, change or perform operations with the value, and then return the new value to the calling program. 

## Return data using a return code
A procedure can return an integer value called a return code to indicate the execution status of a procedure. We specify the return code for a procedure using the `RETURN` statement. As with OUTPUT parameters, we must save the return code in a variable when the procedure is executed in order to use the return code value in the calling program. 

e.g., the assignment variable @result of data type int is used to store the return code from the procedure my_proc, such as:

	DECLARE @result int;  
	EXECUTE @result = my_proc;  

Return codes are commonly used in control-of-flow blocks within procedures to set the return code value for each possible error situation. We can use the `@@ERROR` function after a Transact-SQL statement to detect whether an error occurred during the execution of the statement. Before the introduction of TRY/CATCH/THROW error handling in TSQL, return codes were sometimes required to determine the success or failure of stored procedures. 

However, stored Procedures should always indicate failure with an error (generated with THROW/RAISERROR if neccessary), and not rely on a return code to indicate the failure. Also we should avoid using the return code to return application data.

e.g. In `usp_GetSalesYTD` procedure, we use error handling to set special return code values for various errors. 

![](/images/posts/20180810-sql-6.png)

	IF OBJECT_ID('Sales.usp_GetSalesYTD', 'P') IS NOT NULL  
	    DROP PROCEDURE Sales.usp_GetSalesYTD;  
	GO  
	CREATE PROCEDURE Sales.usp_GetSalesYTD  
	@SalesPerson nvarchar(50) = NULL,  -- NULL default value  
	@SalesYTD money = NULL OUTPUT  
	AS    
	
	-- Validate the @SalesPerson parameter.  
	IF @SalesPerson IS NULL  
	   BEGIN  
	       PRINT 'ERROR: You must specify a last name for the sales person.'  
	       RETURN(1)  
	   END  
	ELSE  
	   BEGIN  
	   -- Make sure the value is valid.  
	   IF (SELECT COUNT(*) FROM HumanResources.vEmployee  
	          WHERE LastName = @SalesPerson) = 0  
	      RETURN(2)  
	   END  
	-- Get the sales for the specified name and   
	-- assign it to the output parameter.  
	SELECT @SalesYTD = SalesYTD   
	FROM Sales.SalesPerson AS sp  
	JOIN HumanResources.vEmployee AS e ON e.BusinessEntityID = sp.BusinessEntityID  
	WHERE LastName = @SalesPerson;  
	-- Check for SQL Server errors.  
	IF @@ERROR <> 0   
	   BEGIN  
	      RETURN(3)  
	   END  
	ELSE  
	   BEGIN  
	   -- Check to see if the ytd_sales value is NULL.  
	     IF @SalesYTD IS NULL  
	       RETURN(4)   
	     ELSE  
	      -- SUCCESS!!  
	        RETURN(0)  
	   END  
	-- Run the stored procedure without specifying an input value.  
	EXEC Sales.usp_GetSalesYTD;  
	GO  
	-- Run the stored procedure with an input value.  
	DECLARE @SalesYTDForSalesPerson money, @ret_code int;  
	-- Execute the procedure specifying a last name for the input parameter  
	-- and saving the output value in the variable @SalesYTD  
	EXECUTE Sales.usp_GetSalesYTD  
	    N'Blythe', @SalesYTD = @SalesYTDForSalesPerson OUTPUT;  
	PRINT N'Year-to-date sales for this employee is ' +  
	    CONVERT(varchar(10), @SalesYTDForSalesPerson);  

Then, we create a program to handle the return codes that are returned from the `usp_GetSalesYTD` procedure.

	-- Declare the variables to receive the output value and return code   
	-- of the procedure.  
	DECLARE @SalesYTDForSalesPerson money, @ret_code int;  
	
	-- Execute the procedure with a title_id value  
	-- and save the output value and return code in variables.  
	EXECUTE @ret_code = Sales.usp_GetSalesYTD  
	    N'Blythe', @SalesYTD = @SalesYTDForSalesPerson OUTPUT;  
	--  Check the return codes.  
	IF @ret_code = 0  
	BEGIN  
	   PRINT 'Procedure executed successfully'  
	   -- Display the value returned by the procedure.  
	   PRINT 'Year-to-date sales for this employee is ' + CONVERT(varchar(10),@SalesYTDForSalesPerson)  
	END  
	ELSE IF @ret_code = 1  
	   PRINT 'ERROR: You must specify a last name for the sales person.'  
	ELSE IF @ret_code = 2   
	   PRINT 'EERROR: You must enter a valid last name for the sales person.'  
	ELSE IF @ret_code = 3  
	   PRINT 'ERROR: An error occurred getting sales value.'  
	ELSE IF @ret_code = 4  
	   PRINT 'ERROR: No sales recorded for this employee.'     
	GO

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

# Demo 1
	CREATE PROCEDURE uspFindContact @LastName NVARCHAR(50), @ContactID INT output 
	AS 
	SELECT TOP 1 @ContactID = c.ContactID 
	   FROM HumanResources.Employee a  
	       INNER JOIN HumanResources.EmployeeAddress b ON a.EmployeeID = b.EmployeeID 
	       INNER JOIN Person.Contact c ON a.ContactID = c.ContactID 
	       INNER JOIN Person.Address d ON b.AddressID = d.AddressID 
	WHERE c.LastName = @LastName

# Demo 2
	CREATE PROCEDURE uspGetContact
		@LastName NVARCHAR(50) 
	AS 
	
	-- declare variables
	DECLARE
		@ContactID INT 
	-- set variables value 
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

[https://docs.microsoft.com/en-us/sql/relational-databases/stored-procedures/return-data-from-a-stored-procedure?view=sql-server-2017](https://docs.microsoft.com/en-us/sql/relational-databases/stored-procedures/return-data-from-a-stored-procedure?view=sql-server-2017)