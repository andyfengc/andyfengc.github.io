---
layout: post
title: ASP.NET Core Tutorial (4) Entity Framework Core
author: Andy Feng
---

# Update #
update EF Core tools, vs -> Tools –> NuGet Package Manager –> Package Manager Console

Install-Package Microsoft.EntityFrameworkCore.Tools -Version 2.1.4

reverse database to entities

Scaffold-DbContext "Data Source=67.224.94.82,4108; Initial Catalog=TweebaaScheduler; MultipleActiveResultSets=True; User Id=wnTweebaa; Password=Tweebaawebapp2014!2016" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Entities\Tweebaa
