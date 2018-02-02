---
layout: post
title: Hangfire tutorial
author: Andy Feng
---

[Download source](/download/angular1-demo.zip)

## Overview ##

Scheduling jobs in Web applications is a challenge, and you can choose from many frameworks for the task. A popular open source library, Hangfire is one framework that can be used for scheduling background jobs in .Net.

There are many job scheduling frameworks available today. Why then should you use Hangfire instead of, say, Quartz.Net, which is another popular framework that has long been in use? Well, one of the major drawbacks of Quartz.Net is that it needs a Windows Service. On the contrary, you don't need a Windows Service to use Hangfire in your application. The ability to run without a Windows Service makes Hangfire a good choice over Quartz.Net. Hangfire takes advantage of the request processing pipeline of ASP.Net for processing and executing jobs.


- [Hangfire](https://www.hangfire.io): an open-source framework that helps us to create, process and manage background jobs

- [Quartz.NET](https://www.quartz-scheduler.net): a full-featured, open source job scheduling system that can be used from smallest apps to large scale enterprise systems.

- FluentScheduler(https://github.com/fluentscheduler/FluentScheduler): a .NET library to create automated job scheduler with fluent interface

- Telerik .NET Scheduler(https://www.telerik.com/products/aspnet-ajax/scheduler.aspx): a ASP.NET library to schedule jobs of data source component, Web service, WCF service, and OData.

- and more


Hangfire is  an open source job scheduling framework to schedule fire-and-forget, recurring tasks in Web applications sans the need of a Windows Service.



Note that Hangfire is not limited to Web applications; you can also use it in your Console applications. The documentation for Hangfire is very detailed and well structured, and the best feature is its built-in dashboard. The Hangfire dashboard shows detailed information on jobs, queues, status of jobs, and so on.

