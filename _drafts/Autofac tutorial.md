---
layout: post
title: Autofac Tutorial
author: Andy Feng
---

# Introduction #

# Workflow #
1. add autofac package via nuget

1. create an IOC container

		var builder = = new ContainerBuilder();
		var container = builder.Build();

1. register components

		builder.RegisterType<ConcretClass1>().As<Interface1>().AsSelf(); 
		builder.RegisterType<ConcretClass2>().As<Interface2>().AsSelf(); 
		...
	
	Please note that Autofac will automatically create an instance for us. If we hope to create a customized instance and inject. This feature is very useful for testing. e.g. we manually inject an instance. 

		var class1Instance = new ConcretClass1();
		// set property values of this instance...
		builder.RegisterInstance(class1Instance).As<Interface1>();

1. get component instanced from container

		var instance1 = container.Resolve<Interface1>();
		var instance2 = container.Resolve<Interface2>();
 
# Webapi demo #
1. create a .net 4.6 webapi project

1. nuget > install `Autofac` `Autofac.integration.WebAPI`

	![](/images/posts/20180308-autofac-1.png)

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
		
		            //Register components
		            builder.RegisterType<ConcretClass1>()
		                .As<Interface1>()
		                .AsSelf()
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

1. In each controller, service, dao class, modify the contrustor to declare all arguments. These declared arguments will be identified by autofac and injected instances automatically.

		public class BaseController : ApiController
	    {
	        protected Interface1 instance1;
	        protected Interface2 instance2;
	        public BaseController(Interface2 instance1
	            , Interface2 instance2)
	        {
	            this.instance1 = instance1;
	            this.instance2 = instance2;
	        }
			...
		}

# FAQ #
1. what is the default constructor where there are multiple constructors.

	the constructor with the most arguments

1. How to explicitly choose a constructor when Autofac build a contrainer?

	1. method1 - by typeof parameter: 

			builder.RegisterType<ConcretClass>()
				.UsingConstructor(typeof(Parameter1Type), typeof(Parameter2Type));

	1. method2 - by IComponentContext lambda expression argument match

			builder.Register(c => new Engine(c.Resolve<Parameter1Type>(), c.Resolve<Parameter2Type>());

# References #
[http://www.c-sharpcorner.com/article/using-autofac-with-web-api/](http://www.c-sharpcorner.com/article/using-autofac-with-web-api/)