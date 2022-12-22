---
layout: post
title: T-SQL Tutorial (9) - FAQ
author: Andy Feng
---

# How to turn a Comma Delimited String to a List of Strings with Single Quotes
e.g. 

	'111,222,333,444'

turn it into something like this:

	'111','222','333','444'

solution: 

	'''' + REPLACE(TheString,  ',', ''',''') + ''''

# How to turn a Comma Delimited String to a List?
way1: Split function using loop

IF Object_id('ufn_Split_String') IS NOT NULL 
  DROP FUNCTION ufn_Split_String 

go 

CREATE FUNCTION ufn_Split_String
(
    @in_string VARCHAR(MAX),
    @delimiter VARCHAR(1)
)
RETURNS @list TABLE(value VARCHAR(100))
AS
BEGIN
        WHILE LEN(@in_string) > 0
        BEGIN
            INSERT INTO @list(value)

            SELECT left(@in_string, charindex(@delimiter, @in_string+@delimiter) -1) as value

            SET @in_string = stuff(@in_string, 1, charindex(@delimiter, @in_string + @delimiter), '')
        end
    RETURN 
END

--test
-- select * from ufn_Split_String('aaa|bbb|ccc|ddd|', '|')
-- SELECT * FROM ufn_Split_String('1001,1002,1003,1004', ',')
	 
we got:

![](/images/posts/20210316-sql-1.jpg)

# The difference between function and stored procedure:

- Function returns only one value but procedure can return one or more than one value.
- Function can be used in select statements but procedure cannot be used.
- Function has only input parameters while Procedure can have an input and output parameters.
- Exceptions can be handled by try catch block in procedures but that is not possible in function.

# difference between clustered and non-clustered index?
Clustered Index: A clustered index is a particular type of index that reorders the way records in the table are physically stored. It gives a sequence of data which is physically stored in the database. Therefore a table can have only one clustered index. The leaf nodes of a clustered index contain the data pages. Index id of the clustered index is 0. So a primary key constraint automatically creates a clustered index.

Non-clustered Index: A non-clustered index is a particular type of index in which the logical order of the index does not match the physically stored order of the rows on disk. In non-clustered index data and indexes are stored in different places. The leaf node of a non-clustered index does not consist of the data pages. Instead, the leaf nodes contain index rows. Index id of non-clustered indexes is greater than 0.


# Reference
[Converting commas or other delimiters to a Table or List in SQL Server using T-SQL](https://www.sqlshack.com/converting-commas-or-other-delimiters-to-a-table-or-list-in-sql-server-using-t-sql/)

[Arrays and Lists in SQL Server](http://www.sommarskog.se/arrays-in-sql.html)

[Arrays and Lists in SQL Server - long version](http://www.sommarskog.se/arrays-in-sql-2005.html)

[SQL Server - Replacing Single Quotes and Using IN](https://stackoverflow.com/questions/1609657/sql-server-replacing-single-quotes-and-using-in)

[sql server interview questions](https://www.javatpoint.com/sql-server-interview-questions)

[SQL Interview Questions and Answers](https://www.javatpoint.com/sql-interview-questions)
