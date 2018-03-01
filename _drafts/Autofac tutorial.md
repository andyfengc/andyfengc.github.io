---
layout: post
title: ASP.NET Core WebApi Tutorial
author: Andy Feng
---

# Webapi #
1. create a .net 4.6 bwebapi project

1. nuget > install `Autofac` `Autofac.integration.WebAPI`

1. create App_Start\AutofacWebapiConfig

		using Autofac;
		using Autofac.Integration.WebApi;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Reflection;
		using System.Web;
		using System.Web.Http;
		
		namespace Tweebaa.Api.App_Start
		{
		    public class AutofacWebapiConfig
		    {
		        public static IContainer Container;
		
		        public static void Initialize(HttpConfiguration config)
		        {
		            Initialize(config, RegisterServices(new ContainerBuilder()));
		        }
		
		
		        public static void Initialize(HttpConfiguration config, IContainer container)
		        {
		            config.DependencyResolver = new AutofacWebApiDependencyResolver(container);
		        }
		
		        private static IContainer RegisterServices(ContainerBuilder builder)
		        {
		            //Register your Web API controllers.  
		            builder.RegisterApiControllers(Assembly.GetExecutingAssembly());
		
		            builder.RegisterType<Entity>()
		                .As<DbContext>()
		                .InstancePerRequest();
		
		            //Set the dependency resolver to be Autofac.  
		            Container = builder.Build();
		
		            return Container;
		        }
		    }
		}

1. global.asax

		public class WebApiApplication : System.Web.HttpApplication
		    {
		        protected void Application_Start()
		        {
		            GlobalConfiguration.Configure(WebApiConfig.Register);
		            //Configure AutoFac  
		            AutofacWebapiConfig.Initialize(GlobalConfiguration.Configuration);
		        }
		    }
# References #
[http://www.c-sharpcorner.com/article/using-autofac-with-web-api/](http://www.c-sharpcorner.com/article/using-autofac-with-web-api/)