---
layout: post
title: DevOps Tutorial
author: Andy Feng
---

# Introduction
`DevOps` is the combination of Development and Operations. It is a culture to encourage the development team and operation team work collaboratively. `DevOps` allows a single team to handle the entire application lifecycle, from development to testing, to deploy, and to  operations. `DevOps` helps you to reduce the disconnection between software developers, quality assurance (QA) engineers, and system administrators.
> `DevOps` deploy code to production faster in an automated & repeatable way..
> `DevOps` focus on rapid IT service delivery, i.e. integration

![](/images/posts/20221209-devops-1.png)

# Why need DevOps
Development and operations both play essential roles in order to deliver applications. Typically, development comprise analyzing the requirements, designing, developing, and testing. Operation consists of administrative processes, services, and support for the software. 

- The operation and development team worked in complete isolation.
- After the design-build, the testing and deployment are performed respectively. That's why they consumed more time than actual build cycles.
- Without the use of DevOps, the team members are spending a large amount of time on designing, testing, and deploying instead of building the project.
- Manual code deployment leads to human errors in production.
- Coding and operation teams have their separate timelines and are not in sync, causing further delays.

# DevOps architecture
DevOps architecture is the solution to fix the gap between deployment and operation teams; therefore, delivery can be faster.

![](/images/posts/20221209-devops-2.png)

> Build: use cloud service to handle hardware allocation.
> 
> Code: user Git to manage code
> 
> Test: user automated test cases
> 
> Plan: use Agile methodology to plan the development
> 
> Monitor: use 3rd tools such as Splunk to make continuous monitor logs
> 
> Deploy: use automated deployment
> 
> Operate: collaboratively participation
> 
> Release: automated release

The main principles of DevOps are Continuous delivery, automation, and fast reaction to the feedback.
> set up configuration
> continuous delivery
> automation

# DevOps Tools
There are quite a few tools to encourage DevOps practice.

![](/images/posts/20221209-devops-3.png)

- Infrastructure Automation: i.e. Amazon Web Services (AWS), Azure. The cloud services can easily scale on demand. It can be configured to provide more servers based on traffic automatically.
- Configuration automation: `Chef` is a handy DevOps tool for achieving speed, scale, and consistency. It can be used to perform configuration management. DevOps team don't have to make changes across ten thousand servers. Rather, they need to make changes in one place, which is automatically reflected in other servers.
- Test: `Docker` for running distributed applications on multiple systems
- Deployment automation: `Jenkins` facilitates continuous integration, or `Ansible`
- Performance management: `App Dynamic` offers real-time performance monitoring.
- Log management: `Splunk` can store and analyze all logs.
- Version control: `Git`

# Best practice
The most common tasks in DevOps are build automation or continuous integration, test automation, and deployment automation.

# FAQ
## DevOps vs Agile
Both DevOps and Agile are software development methodologies with similar objectives, they try to make the deliverable quickly and efficiently as possible. 

Here are difference:

1. `DevOps` is snothing but a practice to make developers and operations team work together. `Agile` refers to iterative, incremental development approach, which breaks final product into smaller, actionable pieces and integrates them.
1. DevOps focus on constant testing and fast delivery. `Agile` focus on constant changes of development.
2. DevOps is used for large team as it involves all stack holders. `Agile` is for small team and fewer people work on it so that they can move faster.
3. `DevOps` focus on collaboration and doesn't have framework. `Agile` can use frameworks such as safe, scrum, and sprint.
4. `DevOps` ideally can deliver code to production daily or fewer hours. `Agile` time can be  2-3 weeks for each iteration.
5. `DevOps` can use tools like Puppet, Chef, AWS, Ansible, Jenkins and team city OpenStack. `Agile` tools like JIRA, Kanboard, Bugzilla.
6. `DevOps` emphasizes Automation and maximize efficiency when deploying software. `Agile` doesn't emphasize automation.
7. `DevOps` involves specification and design documents. `Agile` emphasizes Scrum meeting in daily basis.

# Reference
[DevOps Tools](https://www.javatpoint.com/devops-tools)

[DevOps vs Agile](https://www.javatpoint.com/devops-vs-agile)

[Top 30 DevOps Interview Questions](https://www.javatpoint.com/devops-interview-questions)