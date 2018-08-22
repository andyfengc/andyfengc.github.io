---
layout: post
title: T-SQL Tutorial (3) - sample scripts
author: Andy Feng
---

# Case 1 #
Each employee could have multiple levels, e.g. CP2, CP3, CP4.... Employee in lower level must report to one and only one  direct manager. e.g. John is in CP2 reports to Michael in CP3. 

![](/images/posts/20180810-sql-7.png)

in ERD, 

- `tbl_Employee` saves employee info
- `tbl_Level` saves level and their hierachy info, `DirectReportToLevel` is self-referential
- `tbl_Employee_Level_Rel` saves employee and their level history
- `tbl_Employee_ReportTo_Rel` saves employee and their direct manager history
>

1. get latest level of an employee

	way1:
	
		SELECT lr.EmployeeID, lr.LevelID, lr.[From]
		FROM (
			SELECT lr.EmployeeID, MAX(lr.[From]) maxFrom
			FROM [dbo].tbl_Employee_Level_Rel AS lr
			GROUP BY lr.EmployeeID	 
		) AS lg 
		INNER JOIN dbo.tbl_Employee_Level_Rel lr 
		ON lr.EmployeeID = lg.EmployeeID 
		AND lr.[From] = lg.maxFrom
	
	way2:
	
		SELECT EmployeeID, LevelID, [From]
		FROM dbo.tbl_Employee_Level_Rel
		WHERE [From] = 
			(SELECT MAX([From]) FROM dbo.tbl_Employee_Level_Rel AS lr2 
			WHERE tbl_Employee_Level_Rel.EmployeeID = lr2.EmployeeID)

1. input an employee id. output a list of all report-to direct managers, need employee_id, employee name

	create a stored procedure

		-- get all report-to direct managers of an employee
		IF object_id('uspGetAllReportTo', 'P') IS NOT NULL
		DROP PROCEDURE  uspGetAllReportTo;
		go
		CREATE PROCEDURE uspGetAllReportTo
			@employeeId int
		AS 
			-- DECLARE @employeeId INT -- hard code an employee id
			DECLARE @employeeLevelId INT -- temp variable
			DECLARE @reportToLevelId INT -- temp variable
			DECLARE @reportToEmployeeIds TABLE(employeeId INT) -- temp list of employee ids
			DECLARE @employeeNameFactName NVARCHAR(20)='EmployeeName' -- fact name literal string
			--SET
			--@employeeId = 61389 -- hard code an employee id
			BEGIN
				-- get latest level
				SELECT TOP 1 @employeeLevelId=LevelID
				FROM dbo.tbl_Employee_Level_Rel 
				WHERE EmployeeID = @employeeId
				ORDER BY [From] DESC	
				PRINT @employeeLevelId
		
				-- get report to level
				SELECT @reportToLevelId = DirectReportToLevel 
				FROM dbo.tbl_Level
				WHERE LevelID = @employeeLevelId
				PRINT @reportToLevelId
		
				-- get report to managers' employee ids
				INSERT INTO @reportToEmployeeIds
					SELECT lr.EmployeeID--, lr.LevelID
					FROM (
						SELECT lr2.EmployeeID, MAX(lr2.[From]) maxFrom
						FROM [dbo].tbl_Employee_Level_Rel AS lr2
						-- WHERE lr2.EmployeeID = '56752'
						GROUP BY lr2.EmployeeID
					) AS ge 
					INNER JOIN dbo.tbl_Employee_Level_Rel lr 
					ON lr.EmployeeID = ge.EmployeeID 
					AND lr.[From] = ge.maxFrom
					AND lr.LevelID = @reportToLevelId
				-- print
				-- SELECT * FROM @reportToEmployeeIds;
		
				-- select latest name of managers
				SELECT fg.EmployeeID AS ReportTo, fr2.FactValue
				FROM(
					SELECT fr.EmployeeID, fr.FactID, MAX(fr.[From]) maxFrom
					FROM dbo.tbl_Employee_Fact_Rel fr	
					GROUP BY fr.EmployeeID, fr.FactID
				) AS fg
				INNER JOIN dbo.tbl_Employee_Fact_Rel fr2
				ON fr2.EmployeeID = fg.EmployeeID
				AND fr2.FactID = fg.FactID
				AND	fr2.[From] = fg.maxFrom
				INNER JOIN dbo.tbl_Fact f
				ON fr2.FactID = f.FactID
				AND f.FactName = @employeeNameFactName
				WHERE fr2.EmployeeID IN (SELECT * from @reportToEmployeeIds)
				ORDER BY fr2.FactValue
			END
		GO
	
	EXECUTE sp:

		`EXEC uspGetAllReportTo @employeeId=123456`

# References #
[Stored Procedures](https://docs.microsoft.com/en-us/sql/relational-databases/stored-procedures/stored-procedures-database-engine?view=sql-server-2017)

[SQL Server Stored Procedure Tutorial](https://www.mssqltips.com/sqlservertutorial/160/sql-server-stored-procedure-tutorial/)

[SQL Tutorial](https://www.techonthenet.com/sql/index.php)