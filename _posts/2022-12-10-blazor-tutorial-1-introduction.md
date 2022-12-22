---
layout: post
title: Blazor tutorial 1 - Introduction
author: Andy Feng
---

# Introduction
Blazor is a Single Page Application framework. It is [open source](https://github.com/dotnet/aspnetcore/tree/master/src/Components) and powered by [.NET Foundation](https://dotnetfoundation.org/). Blazor allows us to develop front-end web applications using C#, HTML, razor templates. We use blazor to build components and compose those components into pages which we can use inside our applications. Blazor can run on a server or directly on a client.
> Razor is an ASP.NET programming syntax used to create dynamic web pages using C# or VB.NET languages. Razor was developed in 2010. Razon syntax is essentially template markup syntax. We use razor to create UI components of Blazor web apps.

Blazor doen't require any plugin installed in the client's browser. It runs in the browser by utilizing WebAssembly. Also, It can run in the server side as well. 

Blazor was released as a part of .NET Core 3

There are 2 hosting models: server-side blazor and Web Assembly. 

## Blazor Server side
Blazor server-side pre-renders HTML content before it is sent to the client's browser. Also, Blazor server-side apps will work on older browsers (such as Internet Explorer 11) as there is no requirement for Web Assembly, only HTML and JavaScript. As the code executes on the server, it is also possible to debug our .NET code in Visual Studio.

However, Blazor server-side sets up an in-memory session for the current client and uses `SignalR` to communicate between the .NET running on the server and the client's browser. All memory and CPU usage comes at a cost to the server, for all users. It also means that the client is tied to the server that first served it, so doesn't work with load-balancing.

Once the initial page has been rendered and sent to the browser, `blazor.server.js` file hooks into user interaction events in the browser so it can mediate between the user and the server. For example, if a rendered element has an `@onclick` event registered, `blazor.server.js` will hook into its JavaScript onclick event and then use its SignalR connection to send that event to the server and execute the relevant .NET code.

## WebAssembly(WASM)
WebAssembly is an instruction set that can interprete higher language like C# into machine binary code then execute. It is similar to CIL(common intermediate language). 

![](/images/posts/20221216-blazor-1.png)

WebAssembly runs on the client, inside the browser. Blazor can work offline, when the network connection to the server lost, the client app can continue to function. Also, it can run as Progressive Web App(PWA), which means the client can choose to install out app onto their device and run whatever they wish without any network access at all.

However, `blazor.webassembly` file has to bootstrap the client application. It downloads all required .NET DLL assemblies and it makes the start-up time slower than server-side(DLLs are cached by the browser, making subsequent start-up time faster)

# Demo
1. Create a new project > 

	![](/images/posts/20221216-blazor-2.jpg)

1. 2 types of projects:

	server model: 1 project will be created.

	client model:

	![](/images/posts/20221216-blazor-3.jpg)

	if check "ASP.NET Core Hosted", 3 projects will be created: Client, Server, Shared, the reason is backend project was created.

	if not check, only 1 project created.

1. Create razor component

	right click > razor component > xxx.razor file

	at compile time, each razor component is built into a .NET class. The class inlcudes common UI elements like state, rendering logic, lifecycle methods, and event handlers.
	
	Razor component file can mix HTML and C#

	use @() to add C#  statement inline with HTML. 

	use @code directive to add multiple statements, enclosed by parenthese

	use @functions section to the template for methods and properties

	use @page directive to identify a compnent as a page, also, can be used to specify a route.

# Reference
[Blazor Tutorial - Build your first Blazor app - server-side blazor](https://dotnet.microsoft.com/en-us/learn/aspnet/blazor-tutorial/intro)

[Build a web app with Blazor](https://learn.microsoft.com/en-us/training/modules/build-blazor-webassembly-visual-studio-code/)

[ASP.NET Core Blazor](https://learn.microsoft.com/en-us/aspnet/core/blazor/?view=aspnetcore-7.0)

[Project structure for Blazor apps](https://learn.microsoft.com/en-us/dotnet/architecture/blazor-for-web-forms-developers/project-structure)

[Blazor Interview Questions](https://www.c-sharpcorner.com/article/blazor-interview-questions/)