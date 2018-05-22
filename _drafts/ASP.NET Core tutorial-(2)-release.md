---
layout: post
title: ASP.NET Core Tutorial (2) release
author: Andy Feng
---

# Release via cli #

ASP.NET Core project is actually console application. Our project always contains a Program.cs file just like a console app. 

1. modify program.cs, add IIS or Kestrel integration

		public class Program
		{
		    public static void Main(string[] args)
		    {
		        var host = new WebHostBuilder()
					// kestrel
		            .UseKestrel()
		            .UseContentRoot(Directory.GetCurrentDirectory())
					// iis
		            .UseIISIntegration()
		            .UseStartup()
		            .Build();
		
		        host.Run();
		    }
		}

1. command line > `dotnet publish`

	a folder `publish` will be created

	![](/images/posts/20180228-.netcore-webapi-18.png)

1. IIS > create a new website > copy the `publish` folder > set .net core as runtime