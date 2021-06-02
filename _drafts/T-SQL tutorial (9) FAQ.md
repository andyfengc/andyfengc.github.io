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

# Reference
[Converting commas or other delimiters to a Table or List in SQL Server using T-SQL](https://www.sqlshack.com/converting-commas-or-other-delimiters-to-a-table-or-list-in-sql-server-using-t-sql/)

[Arrays and Lists in SQL Server](http://www.sommarskog.se/arrays-in-sql.html)

[Arrays and Lists in SQL Server - long version](http://www.sommarskog.se/arrays-in-sql-2005.html)

[SQL Server - Replacing Single Quotes and Using IN](https://stackoverflow.com/questions/1609657/sql-server-replacing-single-quotes-and-using-in)



