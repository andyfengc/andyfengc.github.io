---
layout: post
title: T-SQL Tutorial (7) - pagination
author: Andy Feng
---

# way1 - prior to sql server 2012
	DECLARE @Table TABLE(
	        Val VARCHAR(50)
	)
	
	DECLARE @PageSize INT, 
	        @PageNumber INT -- start from 1
	
	SELECT  @PageSize = 10,
	        @Page = 2
	
	;WITH PageNumbers AS(
	        SELECT Val,
	                ROW_NUMBER() OVER(ORDER BY Val) RowNum
	        FROM    @Table
	)
	SELECT  *
	FROM    PageNumbers
	WHERE   RowNum  BETWEEN ((@PageNumber - 1) * @PageSize + 1)
	        AND (@PageNumber * @PageSize)

or

	;WITH PageNumbers AS(
	        SELECT top (@PageNumber * @PageSize) Val,
	                ROW_NUMBER() OVER(ORDER BY Val) RowNum
	        FROM    @Table
	)
	select  * from PageNumbers 
	where RowNum > ((@PageNumber - 1) * @PageSize)


# way2 - sql server 2012+
In SQL Server 2012, the T-SQL syntax has been updated introducing keywords that facilitate a simpler and more efficient paging, keywords such as `OFFSET`, `FETCH`, `NEXT ROWS` and `ONLY`. 

e.g. 2008 version:

	DECLARE	@Offset		AS INT = 6
	DECLARE @PageSize	AS INT = 5
	
	SELECT	Id,
			Name
	FROM
	(
		SELECT	Id,
				Name,
				ROW_NUMBER()	OVER (ORDER BY Id)	AS	RowNumber
		FROM	Users
	) UsersSelection

	WHERE	UsersSelection.RowNumber >  @Offset
	AND	UsersSelection.RowNumber <= @Offset + @PageSize

equivalent to 2012 version:s

	DECLARE	@Offset		AS INT = 6
	DECLARE @PageSize	AS INT = 5
	
	SELECT		Id,
				Name
	FROM		Users
	ORDER BY	Id
	OFFSET		@Offset		ROWS
	FETCH NEXT	@PageSize	ROWS ONLY

# Reference
[Introduction to pagination in SQL Server](https://www.sqlshack.com/introduction-pagination-sql-server/)

[New Features for Database Developers in SQL Server 2012: Simpler Paging, Sequences and FileTables](https://www.codeproject.com/Articles/442503/New-features-for-database-developers-in-SQL-Server)



