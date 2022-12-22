---
layout: post
title: Blazor tutorial 2 - Telerik UI
author: Andy Feng
---

# Installation

## method 1: use msi installation

![](/images/posts/20221216-blazor-5.jpg)

it enables vs to create Telerik Blazor application

## method 2: install Telerik Blazor extension, then create Telerik Blazor application

![](/images/posts/20221216-blazor-4.jpg)

![](/images/posts/20221216-blazor-6.jpg)

![](/images/posts/20221216-blazor-8.jpg)

After installation, you got

![](/images/posts/20221216-blazor-7.jpg)

## method 2: use nuget to install libs to existing project; then config Startup.cs
1. add ui components:
	download zip package at [https://www.telerik.com/account/downloads](https://www.telerik.com/account/downloads)
	
	![](/images/posts/20221216-blazor-9.jpg)
	
	![](/images/posts/20221216-blazor-10.jpg)
	
	unzip > vs > manage nuget package source > point to the unzip folder; or simply set as https://nuget.telerik.com/nuget
	
	![](/images/posts/20221216-blazor-11.jpg)
	
	select `Telerik.UI.for.Blazor`
	
	![](/images/posts/20221216-blazor-12.jpg)

1. add css

	if server model > Pages > _Host.cshtml > add 

		<link rel="stylesheet" href="_content/Telerik.UI.for.Blazor.Trial/css/kendo-theme-bootstrap/all.css" />

		<script src="_content/Telerik.UI.for.Blazor.Trial/js/telerik-blazor.js" defer></script>

	if client(webassembly) model > modify wwwroot > index.razor

1. add reference

	if server model > _imports.razor

		@using Telerik.Blazor
		@using Telerik.Blazor.Components

1. if server model > modify Startup.cs or Program.cs

		builder.Services.AddRazorPages();
		builder.Services.AddServerSideBlazor();
	
		builder.Services.AddTelerikBlazor();
	
		builder.Services.AddSingleton<WeatherForecastService>();

1. modify MainLayout.razor > wrap all content into `<TelerikRootComponent>`

# Reference
[Telerik UI for Blazor video](https://learn.telerik.com/learn/course/27/Telerik%2520UI%2520for%2520Blazor)

[blazor-ui demos](https://demos.telerik.com/blazor-ui/?utm_source=tci)

[blazor-ui docs](https://www.telerik.com/support/blazor-ui?utm_source=tci)