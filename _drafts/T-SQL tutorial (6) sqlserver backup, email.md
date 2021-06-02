---
layout: post
title: T-SQL Tutorial (4) - email, backup, schedule
author: Andy Feng
---

SQL server express not support email feature

developer version or above support

# Enable Database Mail

![](/images/posts/20190625-sqlserver-email-1.png)

![](/images/posts/20200907-sqlserver-email-1.png)

![](/images/posts/20200907-sqlserver-email-2.png)

> After pressing Next, if Database Mail has not been enabled previously, a message will pop up saying: The Database Mail feature is not available. Would you like to enable this feature? Clicking on Yes will enable this feature

![](/images/posts/20200907-sqlserver-email-4.png)

![](/images/posts/20200907-sqlserver-email-5.png)

![](/images/posts/20200907-sqlserver-email-6.png)

> Under the Manage Profile Security page, we have two tabs, Public Profiles and Private Profiles. In the Public Profiles tab, we configure the account that will be available to any user or role with access to mail host database (msdb) to send email notifications using that profile. In the Private Profiles tab, we select the users and which profiles they can use, and after that we click on Next to continue

![](/images/posts/20200907-sqlserver-email-7.png)

![](/images/posts/20200907-sqlserver-email-8.png)

![](/images/posts/20200907-sqlserver-email-9.png)

## Send email and attachment
	use [msdb]
	Exec sp_send_dbmail   
	        @profile_name =  '20200907 email' -- profile name
	     ,  @recipients =  'andy@gmail.com' -- target recipient 
	     ,  @subject =  'Database Backups bla bla'  
	     ,  @body =  'body'  
	     ,  @file_attachments =  'c:\delete\Stock.bak' 

or

	use [msdb]
	Exec sp_send_dbmail   
	        @profile_name =  '20200907 email' 
	     ,  @recipients =  'andyinbox3@gmail.com' 
	     ,  @subject =  'Database Backups bla bla'  
	     ,  @body =  'body'  
	     ,  @file_attachments =  'C:\Backup\MSSQL\HangfireTest_FULL_20200907213716.BAK;C:\Backup\MSSQL\Stock_FULL_20200907213716.BAK' 

# Backup
## Common types of backups in SQL Server
1. Full	
	
	Full backup backs up everything. It is the foundation of any kind of backup. It stores all the objects of the database: Tables, procedures, functions, views, indexes etc. Having a full backup, you will be able to easily restore a database in exactly the same form as it was at the time of the backup.

	`BACKUP DATABASE` is the command used to create a full database backup. It requires at least two input parameters: 
	- the database name 
	- the backup device.

	e.g.

		BACKUP DATABASE [SQLShackDemoATC]
		To DISK='f:\PowerSQL\SQLShackDemoATC.BAK'
		WITH FORMAT,
		      MEDIANAME = 'Native_SQLServerBackup',
		      NAME = 'Full-SQLShackDemoATC backup';

	e.g.

		USE CurrencyExchange
		GO
		BACKUP DATABASE [CurrencyExchange]
		TO  DISK = N'G:\DatabaseBackups\CE.bak'
		WITH CHECKSUM;

1. Differential

	Differential database backup is the superset of the last full backup and contains all changes that have been made since the last full backup. So, if there are very few transactions that have happened recently, a differential backup might be small in size, but if you have made a large number of transactions, the differential backup could be very large in size. 

	As a differential backup doesn’t back up everything, the backup usually runs quicker than a full backup. A differential database backup captures the state of the changed extents at the time that backup was created. If you create a series of differential backups, a frequently-updated database is likely to contain different data in each differential. As the differential backups increase in size, restoring a differential backup can significantly increase the time that is required to restore a database. Therefore, it is recommended to take a new full backup, at set intervals, to establish a new differential base for the data.

	`BACKUP DATABASE` command is used with the differential clause to create the differential database backup. It requires three parameters:
	
	- Database name
	- Backup device
	- The DIFFERENTIAL clause

	e.g. 
	
		BACKUP DATABASE [SQLShackDemoATC]
		   To DISK='f:\PowerSQL\SQLShackDemoATC_Diff.BAK'
		   WITH DIFFERENTIAL,
		    MEDIANAME = 'Native_SQLServerDiffBackup',
		    NAME = 'Diff-SQLShackDemoATC backup';

	e.g.
	
		USE CurrencyExchange
		GO
		BACKUP DATABASE [CurrencyExchange]
		TO  DISK = N'G:\DatabaseBackups\CE.bak'
		WITH CHECKSUM;		 
		 
		BACKUP DATABASE [CurrencyExchange]
		   TO  DISK = N'G:\DatabaseBackups\CE.bak'
		   WITH DIFFERENTIAL;
		   WITH CHECKSUM;		 
		GO

1. Transaction log

	The log backup backs up the transaction logs. This backup type is possible only with full or bulk-logged recovery models. A transaction log file stores a series of the logs that provide the history of every modification of data, in a database. A transaction log backup contains all log records that have not been included in the last transaction log backup.

	`BACKUP LOG` command is used to backup the transaction log. It requires the database name, the destination device and the TRANSACTION LOG clause to initiate the transaction log backup.

	e.g.

		BACKUP LOG [SQLShackDemoATC]
		   To DISK='f:\PowerSQL\SQLShackDemoATC_Log.trn'
		   WITH
		   MEDIANAME = 'Native_SQLServerLogBackup',
		    NAME = 'Log-SQLShackDemoATC backup';
		GO

	e.g.
	
		BACKUP LOG [CurrencyExchange]
		   TO  DISK = N'F:\TLogBackups\CE.log';
		GO

> A pre-requisite to creating a transaction log or differential backup is that a full SQL Server database backup must already exist.
> Transaction log and differential backups play well together with full database backups. By taking a sophisticated approach to backups you can achieve a high level of database continuity and insurance/protection from data loss while minimizing backup file storage requirements. You can, for example, schedule a full SQL Server backup every 12 hours, but a differential, much more often, say every 4 hours and finally, back up your transaction log every 15 minutes. The key is finding the sweet spot between mitigating potential data loss and storage requirements that is optimal for your organization

## Verifying backups

SQL Server backup verification includes the following checks

- The backup was created successfully
- It is currently intact, physically and that all the files not only exist but are also readable
- The backup can be restored, when it is needed
- All the transactions are consistent

with T-SQL: include `WITH CHECKSUMS` statement. e.g.

	BACKUP DATABASE [CurrencyExchange]
	TO  DISK = N'G:\DatabaseBackups\CE.bak'
	WITH CHECKSUM;

with SSMS

![](/images/posts/20200907-sqlserver-backup-1.png)

## Sample - full backup multiple databases
	-- fully backup
	
	DECLARE @name VARCHAR(50) -- database name  
	DECLARE @path VARCHAR(256) -- path for backup files  
	DECLARE @fileName VARCHAR(256) -- filename for backup  
	DECLARE @fileDate VARCHAR(20) -- used for file name
	DECLARE @result table(
		filepath varchar(max) not null
	)
	
	-- specify database backup directory
	SET @path = 'C:\Backup\MSSQL\'  
	 
	-- specify filename format
	--SELECT @fileDate = CONVERT(VARCHAR(20),GETDATE(),112) -- i.e. Capstone_20190108.bak
	SELECT @fileDate = CONVERT(VARCHAR(20),GETDATE(),112) + REPLACE(CONVERT(VARCHAR(20),GETDATE(),108),':','') -- Capstone_20190108094532.BAK
	 
	DECLARE db_cursor CURSOR READ_ONLY FOR  
	SELECT name 
	FROM master.dbo.sysdatabases 
	WHERE name IN ('database1','database2')  -- include these databases
	--WHERE name NOT IN ('master','model','msdb','tempdb')  -- exclude these databases
	
	OPEN db_cursor   
	FETCH NEXT FROM db_cursor INTO @name   
	 
	WHILE @@FETCH_STATUS = 0   
	BEGIN   
	   SET @fileName = @path + @name + '_FULL_' + @fileDate + '.BAK'  
	   BACKUP DATABASE @name
		TO DISK = @fileName  
		WITH INIT; 
		INSERT INTO @result(filepath) values(@fileName);
	   FETCH NEXT FROM db_cursor INTO @name   
	END   
	 
	CLOSE db_cursor
	DEALLOCATE db_cursor
	
	select * from @result;

## Sample - Differential backup
	-- differential backup
	
	DECLARE @name VARCHAR(50) -- database name  
	DECLARE @path VARCHAR(256) -- path for backup files  
	DECLARE @fileName VARCHAR(256) -- filename for backup  
	DECLARE @fileDate VARCHAR(20) -- used for file name
	 
	-- specify database backup directory
	SET @path = 'C:\Backup\MSSQL\'  
	 
	-- specify filename format
	--SELECT @fileDate = CONVERT(VARCHAR(20),GETDATE(),112) -- i.e. Capstone_20190108.bak
	SELECT @fileDate = CONVERT(VARCHAR(20),GETDATE(),112) + REPLACE(CONVERT(VARCHAR(20),GETDATE(),108),':','') -- Capstone_20190108094532.BAK
	 
	DECLARE db_cursor CURSOR READ_ONLY FOR  
	SELECT name 
	FROM master.dbo.sysdatabases 
	WHERE name IN ('database1','database2')  -- include these databases
	--WHERE name NOT IN ('master','model','msdb','tempdb')  -- exclude these databases
	
	OPEN db_cursor   
	FETCH NEXT FROM db_cursor INTO @name   
	 
	WHILE @@FETCH_STATUS = 0   
	BEGIN   
	   SET @fileName = @path + @name + '_DIFF_' + @fileDate + '.BAK'  
	   BACKUP DATABASE @name
		TO DISK = @fileName  
		WITH DIFFERENTIAL; 
	   FETCH NEXT FROM db_cursor INTO @name   
	END   
	
	CLOSE db_cursor   
	DEALLOCATE db_cursor

## Demo 3 - Check backups
	
	;with backup_cte as
	(
	    select
	        database_name,
	        backup_type =
	            case type
	                when 'D' then 'database'
	                when 'L' then 'log'
	                when 'I' then 'differential'
	                else 'other'
	            end,
	        backup_finish_date,
	        rownum = 
	            row_number() over
	            (
	                partition by database_name, type 
	                order by backup_finish_date desc
	            )
	    from msdb.dbo.backupset
	)
	select
	    database_name,
	    backup_type,
	    backup_finish_date
	from backup_cte
	where rownum = 1
	order by database_name;

# Create Schedule job
1. Start sql server agent service

	![](/images/posts/20200907-sqlserver-backup-3.png)

1.  Configure SQL Server Agent to use Database Mail. Optional
	
	SSMS > Object Explorer> right click on SQL Server Agent > click on Properties

	![](/images/posts/20200907-sqlserver-backup-2.png)

	SQL Server Agent Properties dialog > select Alert System from the navigation tab > check the box near Enable mail profile > restart the SQL Server Agent service

	![](/images/posts/20200907-sqlserver-backup-4.png)

1. schedule an automated backup

	 SQL Server Agent > Right click Jobs > select New job from the context menu

	![](/images/posts/20200907-sqlserver-backup-5.png)

	![](/images/posts/20200907-sqlserver-backup-6.png)

1. If we have a predefined backup stored procedure `usp_Backup_And_SendEmail` > steps > new > select the saved stored procedure

	![](/images/posts/20200907-sqlserver-backup-7.png)

	![](/images/posts/20200907-sqlserver-backup-8.png)

1. schedules > new a schedule

	![](/images/posts/20200907-sqlserver-backup-9.png)

	![](/images/posts/20200907-sqlserver-backup-10.png)

1. That's it. We already scheduled the stored procedure to backup Stock database everyday at 12am.
	
	Open SQL Server Management Studio > SQL Server Agent > Job Activity Monitor to check the job status

	![](/images/posts/20200907-sqlserver-backup-11.png)

1. test the job

	

# FAQ
1. email attachment limitation error: File attachment or query results size exceeds allowable value of 1000000 bytes

	![](/images/posts/20200907-sqlserver-email-10.png)

	We can check the SQL Server Database Mail parameter properties by running the following stored procedure: sysmail_help_configure_sp.

	`exec msdb.dbo.sysmail_help_configure_sp`

	![](/images/posts/20200907-sqlserver-email-11.png)

	1. way1 - Change SQL Server Database Mail Settings with Management Studio
	
		Management > Database Mail and right click and select Configure Database Mail > View or change system parameters

		![](/images/posts/20200907-sqlserver-email-12.png)

		Here we can see the current setting for Maximum File Size (Bytes). The default is 1,000,000 bytes which is 1MB. The maximum value we can use 2,147,483,647, which is roughly 2GB. If we try to use a value greater than 2147483647 it will throw an error. So change the value to the necessary size for your needs.

		![](/images/posts/20200907-sqlserver-email-13.png)

		Another option that we can change is the Prohibited Attachment File Extensions. This will allow us to block specific file extensions as attachments.

		![](/images/posts/20200907-sqlserver-email-14.png)

	1. way2 - Change SQL Server Database Mail Settings with T-SQL

			exec msdb.dbo.sysmail_configure_sp 'MaxFileSize','2000000'
			exec msdb.dbo.sysmail_configure_sp 'ProhibitedExtensions','exe,dll,vbs,js,ps'

# References
[How to set up email notifications for backup jobs in SQL Server](https://solutioncenter.apexsql.com/how-to-set-up-email-notifications-for-backup-jobs-in-sql-server/)

[Understanding SQL Server Backup Types](https://www.sqlshack.com/understanding-sql-server-backup-types/)

[BACKUP (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/statements/backup-transact-sql?view=sql-server-2017)

[Get last full backup and transaction log backup for each database](https://dba.stackexchange.com/questions/89278/get-last-full-backup-and-transaction-log-backup-for-each-database)

[Multiple methods for scheduling a SQL Server backup automatically](https://www.sqlshack.com/multiple-methods-for-scheduling-a-sql-server-backup-automatically/)