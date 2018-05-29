---
layout: post
title: ASP.NET web project tutorial 1 - .net 4.5
author: Andy Feng
---

# create new empty web application #

![](/images/20160604-create-project-1.png)

![](/images/20160604-create-mvc-project-2.png)

![](/images/20160604-create-mvc-project-3.png)

# Add mvc support #
Open nuget, install mvc library
![](/images/posts/20160604-create-mvc-project-4.png)

Create App_Start folder, create RouteConfig.cs file and FilterConfig.cs file under this folder. Also, create global.asax file

![](/images/posts/20160604-create-mvc-project-5.png)

![](/images/posts/20160604-create-mvc-project-6.png)

![](/images/posts/20160604-create-mvc-project-7.png)

![](/images/posts/20160604-create-mvc-project-8.png)

quick test:

Add Controllers folder, create a new Controller class, HomeController.cs

![](/images/posts/20160604-create-mvc-project-9.png)

Start project, succeed

![](/images/posts/20160604-create-mvc-project-10.png)

# Add webapi support #
Open nuget, install webapi.core, webapi, webapi cors library

![](/images/20160612-create-webapi-project-1.png)

Create App_Start folder, create WebApiConfig.cs file under this folder. Also, create global.asax file

![](/images/20160612-create-webapi-project-2.png)

![](/images/20160612-create-webapi-project-3.png)

quick test:

Add Controllers folder, create a new Controller class, HomeController.cs

![](/images/20160612-create-webapi-project-4.png)

Start project, succeed

![](/images/20160612-create-webapi-project-5.png)

