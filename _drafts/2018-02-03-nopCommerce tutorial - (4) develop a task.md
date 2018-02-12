---
layout: post
title: nopCommerce tutorial - (3) develop a task
author: Andy Feng
---

# Create a simple task #
A custom task can be scheduled to run at certain periods. Here are basic steps to create a new task:

1. In Nop.Services > new folder AndyTasks > Create a new task

	Define a class which implements IScheduleTask interface. There is only one method Execute() which will be invoked when the task runs.

	![](/images/posts/20180211-nop-8.png)	
	
	Note that in nopCommerce 3.x, the interface is ITask.

1. In database > ScheduleTask table > insert a new record for this new task
	
	![](/images/posts/20180211-nop-9.png)

	Note that the type format is "project name fullpath + classname, project name"

1. Rebuild and start the web project

	![](/images/posts/20180211-nop-4.png)

1. login as admin > administration > system > Schedule tasks, we will find the new task

	![](/images/posts/20180211-nop-10.png)

# Create a task as plugin  #
Besides manually insert a new record into the ScheduleTask table as above, we can also use IScheduleTaskService for inserting such a record.

In this case, we want to create a new task as a plugin. Then insert a new task record by installing the plugin. Here are steps:

1. create a new classic .net project MyTasks
	
	![](/images/posts/20180211-nop-12.png)

	Please note the project location should be Plugins folder.

1. specify the project output path: ..\..\Presentation\Nop.Web\Plugins\MyTasks\

	![](/images/posts/20180211-nop-14.png)

1. Create a new task plugin

	As a plugin, a class has to implement IPlugin interface or extend BasePlugin. As a task, a class has to implement IScheduleTask interface. Therefore, we got:

	    public class MyTask : BasePlugin, IScheduleTask
	    {        
	        public void Execute()
	        {
				throw new NotImplementedException();
	        }
	        public override void Install()
	        {
				// todo: some work when installing the plugin				
	            base.Install();
	        }
	        public override void Uninstall()
	        {
				// todo: some work when uninstalling the plugin
	            base.Uninstall();
	        }
	    }

1. Add plugin settings file: plugin.json

		{
		  "Group": "User Plugins",
		  "FriendlyName": "Andy's Tasks",
		  "SystemName": "MyTasks",
		  "Version": "1.02",
		  "SupportedVersions": [ "4.00" ],
		  "Author": "nopCommerce team",
		  "DisplayOrder": 1,
		  "FileName": "MyTasks.dll",
		  "Description": "This task just for demo pupose"
		}

	![](/images/posts/20180211-nop-13.png)

1. Schedule new task

	To schedule a task, we have to insert a row in the ScheduleTask table. We can use the ScheduleService with the InsertTask method, and call it in the Install method of the plugin.

	    public class MyTask : BasePlugin, IScheduleTask
	    {
	        private static int count = 0;
	        private IScheduleTaskService _scheduleTaskService;

	        public MyTask(IScheduleTaskService scheduleTaskService) // inject scheduleTaskService by Autofac
	        {
	            this._scheduleTaskService = scheduleTaskService;
	        }
	        
	        public void Execute()
	        {
				// a simple task to create a file
	            File.WriteAllText("c:/delete/file_" + count++ + ".txt", count + ""); 
	        }
	        public override void Install()
	        {
				// automatically configure this task by inserting a record to ScheduleTask table when installing this plugin
	            _scheduleTaskService.InsertTask(new Nop.Core.Domain.Tasks.ScheduleTask()
	            {
	                Enabled = true,
	                Name = "Sample Task",
	                Seconds = 3600,
	                StopOnError = false,
	                Type = "MyTasks.MyTask",
	            });
	            base.Install();
	        }
	        public override void Uninstall()
	        {
				// automatically remove this task from ScheduleTask table when uninstalling this plugin
	            Nop.Core.Domain.Tasks.ScheduleTask task = _scheduleTaskService.GetTaskByType("MyTasks.MyTask");
	            if (task != null)
	            {
	                _scheduleTaskService.DeleteTask(task);
	            }
	            base.Uninstall();
	        }
	    }

1. Rebuild the plugin project, the complied dlls will be automatically generated.

	![](/images/posts/20180211-nop-15.png)

1. Restart the web project

	![](/images/posts/20180211-nop-4.png)

1. login as admin > administration > configuration > plugins > local plugins, we will find the new task plugin:

	![](/images/posts/20180211-nop-16.png)

	Click install:

	![](/images/posts/20180211-nop-17.png)

	After installation, we can find the new task record added to ScheduleTask table

	![](/images/posts/20180211-nop-18.png)

1. Test the task

	In administration > system > Schedule tasks, we will find the new task 

	![](/images/posts/20180211-nop-19.png)

	Click run now, we will find the task was executed and the file was generated

	![](/images/posts/20180211-nop-20.png)
	
1. Next, we can add settings and configuration and so on.

# Reference #

[http://predicatet.blogspot.ca/2014/01/nopcommerce-how-to-write-custom-task.html](http://predicatet.blogspot.ca/2014/01/nopcommerce-how-to-write-custom-task.html)