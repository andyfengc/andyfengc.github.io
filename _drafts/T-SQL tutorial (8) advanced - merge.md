---
layout: post
title: T-SQL Tutorial (8) - merge, openquery
author: Andy Feng
---

# Merge
`merge` statement was introduced in sql server 2008. It allows us to perform insert, update and delete in one statement. Merge can be used to one-way synchronize source table to target table. 

It requires 2 tables to use merge:

1. soure table -> contains the changes that need to be applied to the target table
1. target table ->  the table that require changes (insert, update and delete)
1. `merge` statement joins the target table to the source table by using a common column in both tables. Based on how the rows match up, we can perform insert, update and delete in one statement.
1. `merge` works well when two tables have a complex mixture of matching. For example, inserting a row if it doesn't exist, or updating a row if it matches. When simply updating one table based on the rows of another table, just use basic INSERT, UPDATE, and DELETE statements. For example:

		INSERT tbl_A (col, col2)  
		SELECT col, col2
		FROM tbl_B
		WHERE NOT EXISTS (SELECT col FROM tbl_A A2 WHERE A2.col = tbl_B.col);  

	OR
	
	

	or

		UPDATE D
		SET
			D.Name=P.Name
			,D.Address=P.Address
			,D.City=P.City
			,D.Province=P.Province
			,D.Phone=P.Phone
		FROM
			dbo.tbl_Company D
			INNER JOIN
			[Target database].dbo.tbl_Company P
			ON
				D.CompanyID=P.CompanyID;

syntax:

	MERGE [target_table]
	USING [source_table]
	ON [join_condition]
	WHEN MATCHED THEN [update statement]
	WHEN NOT MATCHED BY TARGET THEN [insert statement]
	WHEN NOT MATCHED BY SOURCE THEN [delete statement]
	;

## demo 1
![](/images/posts/20210512-sql-1.png)
> use `ID` as join column
> if ID value matched, do update in target; if not matched in target side, do update in target; if not matched in source side, delete from target
> finally, target syncs with source


# Reference
[Part 69 Merge in SQL Server](https://www.youtube.com/watch?v=5dk33HN8BX8)

[MERGE (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15)
