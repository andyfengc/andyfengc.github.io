---
layout: post
title: ASP.NET AOP tutorial
author: Andy Feng
---

## Introduction ##

For instance, below information should be logged for every method in our business logic service:

1. entry into a method
1. parameters passed to the method (name and value)
1. total time of execution
1. parameters returned from the method
1. exit from try block
1. exit from final block
1. exceptions
1. ...

Here is a sample to log information mixed with business logic in our class:

        public int Add(int x, int y)
        {
            int result = 0;
            try
            {
                Console.WriteLine("begin execution in try block of Add()...");
                var parameters = string.Format("input parameters x={0}, y={1}", x, y);
                Console.WriteLine(parameters);
                Stopwatch sw = new Stopwatch();
                sw.Start();
                result = x + y;
                sw.Stop();
                Console.WriteLine("time elapsed {0}", sw.Elapsed);
                var returnValue = string.Format("result {0}", result);
                Console.WriteLine(returnValue);
                Console.WriteLine("end execution in try block of Add()");
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("exception happens: {0}", ex.Message));
            }
            finally
            {
                Console.WriteLine("end execution in final block of Add()");
            }
            return result;
        }

As we see, it is messy and hard to maintain. Business logic is embeded inside the long piece of code and not easy to improve.

Actually, the business logic is as simple as this:

        public int Add(int x, int y)
        {
            return x + y;
        }

Aspect-object programming(AOP) is a good approach to handle this issue. It can be used to separate the concern of non-business logic from the service class and enable us to programming quicker and easier. AOP is based on the conception of interception. There are multiple AOP solutions in C# such as:

1. Unity: a IOC framework supported by Microsoft
1. MEF(Managed Extensibility Framework): a framework to extend .NET framework and used to develop plugin systems
1. Spring.NET: ported from Java Spring framework
1. Autofac: a new IOC and DI framework, very light and high performance
1. Ninject: a popular light IOC framework

Here is the structure of Unity and other framworks are pretty similar:

![](/images/posts/20170928-spring-aop-2.png)

Unity AOP consists of three elements: 

1. Inteceptor (or proxy) - invoke the behaviors via pipeline
2. Behavior pipeline - register the behaviors
3. Behavior of aspect - encapsulates the non-business logic

This workflow includes the client application, interception system(non-business logic) and target(business logic). 

1. Once the client application is configured to use the interception system, any method invocation goes through a proxy object - the interceptor
2. Inteceptor looks the list of registered behaviors and invokes them through the internal pipeline
3. After all behaviors completed, the target is invoked and returned

## Prepare business logic ##

1. create services  
	interface:

		namespace Console.aop
		{
		    public interface IOrderService
		    {
		        void GrabNewOrders();
		    }
		}

	implementation:

		namespace Console.aop
		{
		    public class OrderService : IOrderService
		    {
		        public void GrabNewOrders()
		        {
		            System.Console.WriteLine("Grabbed 100 new orders");
		            System.Console.WriteLine("Saved 100 new orders");
		        }
		    }
		}

## Unity ##

### Install Unity ###

nuget > Unity.Interception

![](/images/posts/20170928-spring-aop-3.png)

### way1: create logging service via configuration - todo ###

	<?xml version="1.0" encoding="utf-8" ?>  
	<configuration>  
	  <configSections>  
	        <section name="unity" type="Microsoft.Practices.Unity.Configuration.UnityConfigurationSection, Microsoft.Practices.Unity.Configuration, Version=2.0.414.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/> 
	  </configSections>  
	  
	  <unity xmlns="http://schemas.microsoft.com/practices/2010/unity">
	    <sectionExtension type="Microsoft.Practices.Unity.InterceptionExtension.Configuration.InterceptionConfigurationExtension, Microsoft.Practices.Unity.Interception.Configuration" />
	    <!-- Using Interception -->
	    <container name="Interception">
	      <extension type="Interception" />
	      <register type="AopTest.ILogger, AopTest" mapTo="AopTest.Logger, AopTest" />
	      <register type="Console.aop.IOrderService, Console" mapTo="Console.aop.OrderService, Console" >
	        <interceptor type="InterfaceInterceptor" />
	        <interceptionBehavior type="Infrastructure.LoggingInterceptionBehavior, Infrastructure" />
	      </register>
	    </container>
	  </unity>
	</configuration>

### way2: create logging service via fluent api - todo ###

## Sprint.NET ##

### Install Spring.net AOP ###

nuget > spring.aop

![](/images/posts/20170928-spring-aop-1.png)

### way1: Create logging service via configuration ###

create a LogBeforeService:

	namespace Console.aop
	{
	    public class LogBeforeService : IMethodBeforeAdvice
	    {
	        public void Before(MethodInfo method, object[] args, object target)
	        {
	            System.Console.WriteLine("intercept method name—>" + method.Name);
	            System.Console.WriteLine("target—>" + target);
	            System.Console.WriteLine("parameter—>");
	            if (args != null)
	            {
	                foreach (object arg in args)
	                {
	                    System.Console.WriteLine("\t: " + arg);
	                }
	            }
	        }
	    }
	}

app.config

	<?xml version="1.0" encoding="utf-8" ?>  
	<configuration>  
	  <configSections>  
	    <sectionGroup name="spring">  
	      <section name="context" type="Spring.Context.Support.ContextHandler, Spring.Core" />  
	      <section name="objects" type="Spring.Context.Support.DefaultSectionHandler, Spring.Core" />  
	    </sectionGroup>  
	  </configSections>  
	  
	  <spring>  
	    <context>  
	      <resource uri="config://spring/objects"/>  
	    </context>  
	  
	    <objects xmlns="http://www.springframework.net">  
	      <description>AOP例子</description>  
	      <object id="beforeAdvice" type="Console.aop.LogBeforeService,Console"/>  
	      <object id="orderService" type="Spring.Aop.Framework.ProxyFactoryObject">  
	        <property name="Target">  
	          <object type="Console.aop.OrderService, Console" />  
	        </property>  
	        <property name="InterceptorNames">  
	          <list>  
	            <value>beforeAdvice</value>  
	          </list>  
	        </property>  
	      </object> 
	  
	  </spring>  
	</configuration>

program.cs

    IApplicationContext context = ContextRegistry.GetContext();
    IOrderService command = (IOrderService)context["orderService"];
    command.GrabNewOrders(); 

### way2: create logging service via fluent syntax ###

create a LogAroundService

	namespace Console.aop

	{
	    public class LogAroundService : IMethodInterceptor
	    {
	        public object Invoke(IMethodInvocation invocation)
	        {
	            System.Console.Out.WriteLine("Advice executing; calling the advised method..."); 
	            object returnValue = invocation.Proceed(); 
	            System.Console.Out.WriteLine("Advice executed; advised method returned " + returnValue); 
	            return returnValue; 
	        }
	    }
	}

program.cs

    ProxyFactory factory = new ProxyFactory(new OrderService());
    factory.AddAdvice(new LogAroundService());
    IOrderService service = (IOrderService)factory.GetProxy();
    service.GrabNewOrders();
 
### summary ###
the drawback is, we can only get parameter as index of array, e.g. way2

	object[] arguments = invocation.Arguments;

It doesn't support strong type and we can only get each parameter of method via index.
## References ##

[http://blog.csdn.net/chinacsharper/article/details/19295103](http://blog.csdn.net/chinacsharper/article/details/19295103)

[http://springframework.net/doc-latest/reference/html/aop-quickstart.html](http://springframework.net/doc-latest/reference/html/aop-quickstart.html)