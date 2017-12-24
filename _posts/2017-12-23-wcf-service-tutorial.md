---
layout: post
title: ASP.NET WCF Service Tutorial 
author: Andy Feng
---

[Download source](/download/wcf-tutorial.zip)

## Introduction ##
WCF(Windows Communication Foundation) is a service layer that allows us to build applications that can communicate using a variety of communication approaches. With it, you can communicate using Peer to Peer, Named Pipes, Web Services and so on.

Each WCF service can be configured as multiple endpoints to provide services. Each endpoint represents a unique channel to provide a certain service.

![](/images/posts/20171221-wcf-structure.jpg)

The major benefit is WCF can reuse the same service code to provide services via multiple protocols such as HTTP, TCP/IP. 

## Tutorial 1: Create a simple WCF service via web hosting ##
### Outline ###
1. create a new a WCF service in a web project
2. implement the service logic
3. configure WCF service contract interface
3. configure the endpoint(s) in web.config
4. host and run
5. consume the service

### Steps ###
#### create a new a WCF service in a web project ####
Create a web project > right click > new item > WCF service

![](/images/posts/20171221-wcf-service-1.png)

A WCF contains one or more interfaces and its implemented classes. two files will be created: 

interface: ISampleWcfService.cs

	[ServiceContract]
	public interface ISampleWcfService
	{
	    [OperationContract]
	    void DoWork();
	}

Please note that [ServiceContract] and [OperationContract] attributes are required for expost the service method.

implementation class: SampleWcfService.svc

    public class SampleWcfService : ISampleWcfService
    {
        public void DoWork()
        {
        }
    }

#### implement the service logic ####
Add a new method and implement the logic. We got

ISampleWcfService.cs

	[ServiceContract]
    public interface ISampleWcfService
    {
        [OperationContract]
        Student GetStudent();
    }

    public class Student
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public DateTime DateOfBirth { get; set; }
    }

SampleWcfService.svc

	public class SampleWcfService : ISampleWcfService
    {
        public Student GetStudent()
        {
            return new Student()
            {
                Name = "Jenny",
                Age = 22,
                DateOfBirth = new DateTime(1995, 3, 7)
            };
        }
    }

Now we can make a test

solution explorer > select SampleWcfService.svc > ctrl + f5, run

![](/images/posts/20171221-wcf-service-2.png)

double click an endpoint > click `Invoke` button

![](/images/posts/20171221-wcf-service-3.png)

We can also view this WCF service via link:

![](/images/posts/20171221-wcf-service-5.png)

### configure WCF service contract interface ###
We can add GET, JSON in WCF service contract interface
 
add attribute to endpoint method

	[ServiceContract]
    public interface ISampleWcfService
    {
        [OperationContract]
        void DoWork();

        [WebGet(UriTemplate = "students", ResponseFormat = WebMessageFormat.Json)]
        //[WebInvoke(Method = "GET", ResponseFormat = WebMessageFormat.Json, UriTemplate = "students")] // equivalent
        [OperationContract]
        Student GetStudent();
    }

### configure the endpoint(s) in web.config ###
Add IIS hosting and enable RESTFUL WCF services
modify web.config

	  <system.serviceModel>
	    <services>
	      <service name="WebApplication1.Wcf.SampleWcfService" behaviorConfiguration="RestServiceBehavior">
	        <endpoint address="" binding="webHttpBinding" contract="WebApplication1.Wcf.ISampleWcfService" behaviorConfiguration="WebEndpointBehavior"/>
			<!--wsdl-->
			<endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
	      </service>
	    </services>
	    <behaviors>
	      <serviceBehaviors>
	        <behavior name="RestServiceBehavior">
	          <serviceMetadata httpGetEnabled="true" httpsGetEnabled="true" />
	          <serviceDebug includeExceptionDetailInFaults="false" />
	        </behavior>
	      </serviceBehaviors>
	      <endpointBehaviors>
	       <behavior name="WebEndpointBehavior">
          	<webHttp />
           </behavior>
	      </endpointBehaviors>
	    </behaviors>
	    ...
	    <client>
	      <endpoint address="http://localhost:60000/Wcf/SampleWcfService.svc"
	        binding="basicHttpBinding" bindingConfiguration="BasicHttpBinding_ISampleWcfService"
	        contract="SampleWcfService.ISampleWcfService" name="BasicHttpBinding_ISampleWcfService" />
	    </client>
	  </system.serviceModel>

1. `<system.serviceModel>`: root of WCF service configuration 
1. `<services>`: specify endpoint(s) for each service. one service could provide multiple endpoints, each endpoint represents a service entry. For instance, we can specify two endpoints: one HTTP endpoint and one TCP endpoint for one service.
1. `<endpoint>`: configure the service details. It conforms to ABC principle and contains three major attributes.
	![](/images/posts/20171221-wcf-service-10.png)
	> - A: address of the service 
	> - B: bind the service to a specific protocol
	> - C: contract defines the methods in service interface
	> e.g. <endpoint address="" binding="webHttpBinding" contract="WebApplication1.Wcf.ISampleWcfService" />
	> e.g. <endpoint address="" binding="netTcpBinding" contract="WebApplication1.Wcf.ISampleWcfService" />
1. `<serviceBehaviors>`: specify the metadata i.e. how to connect the service.

### host and run ###
ctrl + f5 to start the service
We can access service via  `GET http://localhost:60000/Wcf/SampleWcfService.svc/students`

![](/images/posts/20171221-wcf-service-7.png)

### Consume WCF service ###
Once a service is hosted, we can consume it in client applications.

First, we need to create a proxy for the service. This proxy is used by the client application to interact with the service. 

#### way1 to create proxy class ####

project name > services reference > right click > add service reference > ok

![](/images/posts/20171221-wcf-service-4.png)

here we got the proxy class

![](/images/posts/20171221-wcf-service-6.png)

#### way2 to create proxy class ####
Alternatively, we can use service utility in Visual Studio command prompt. We can create the proxy class and its configuration information via: 

	svcutil http://localhost:60000/Wcf/SampleWcfService.svc

After executing this command, we will get two files generated in the default location.

MyService.cs − Proxy class for the WCF service
output.config − Configuration information about the service

#### Create a client application ####
The client could be any .NET application such as web form, mvc, console, windows form or other clients written by other languages. Here, we create a web form to demostrate.

create a new webform, add a few controls

	<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="SampleWebServiceClient.aspx.cs" Inherits="WebApplication1.SampleWebServiceClient" %>
	
	<!DOCTYPE html>
	
	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	    <title></title>
	    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
	</head>
	<body>
	    <form id="form1" runat="server">
	        <div class="container">
	            <table class="table table-hover table-bordered">
	                <tr>
	                    <td colspan="2">
	                        <asp:Button ID="btnLoad" runat="server" Text="Load Student" OnClick="btnLoad_Click" /></td>
	                </tr>
	                <tr>
	                    <td>Name</td>
	                    <td>
	                        <asp:Label ID="lblName" runat="server" Text=""></asp:Label></td>
	                </tr>
	                <tr>
	                    <td>Age</td>
	                    <td>
	                        <asp:Label ID="lblAge" runat="server" Text=""></asp:Label></td>
	                </tr>
	                <tr>
	                    <td>Date of Birth</td>
	                    <td>
	                        <asp:Label ID="lblDob" runat="server" Text=""></asp:Label></td>
	                </tr>
	            </table>
	        </div>
	    </form>
	</body>
	</html>

In the backend

	public partial class SampleWebServiceClient : System.Web.UI.Page
	    {
	        protected void Page_Load(object sender, EventArgs e)
	        {
	
	        }
	
	        protected void btnLoad_Click(object sender, EventArgs e)
	        {
	            var client = new SampleWcfServiceClient();
	            var student = client.GetStudent();
	            lblName.Text = student.Name;
	            lblAge.Text = student.Age.ToString();
	            lblDob.Text = student.DateOfBirth.ToString("yyyy-MM-dd");
	        }
	    }

It looks like

![](/images/posts/20171221-asmx-webservice-10.png)

Please note that when we click the `Load` button, it creates new a proxy instance and help us make the wcf service call. Finally, when we click the button in the page, we got

![](/images/posts/20171221-asmx-webservice-11.png)

## Tutorial 2: create a multiple-tier WCF service ##
Download here
### Outline ###
1. create WCF service
2. add console hosting for web hosting
3. add web hosting
4. consume WCF service

### create WCF service ###
create an empty solution > add services project > right click > create new WCF service

implement service contract interface and service implementation

![](/images/posts/20171221-wcf-service-12.png)

ISampleWcfService

	namespace Services
	{
	    // NOTE: You can use the "Rename" command on the "Refactor" menu to change the interface name "ISampleWcfService" in both code and config file together.
	    [ServiceContract]
	    public interface ISampleWcfService
	    {
	        //[WebGet(UriTemplate = "students", ResponseFormat = WebMessageFormat.Json)]
	        //[WebInvoke(Method = "GET", ResponseFormat = WebMessageFormat.Json, UriTemplate = "students")]
	        [WebGet(ResponseFormat = WebMessageFormat.Json, UriTemplate = "students")]
	        [OperationContract]
	        Student GetStudent();
	
	    }
	    [DataContract]
	    public class Student
	    {
	        [DataMember]
	        public string Name { get; set; }
	        [DataMember]
	        public int Age { get; set; }
	        [DataMember]
	        public DateTime DateOfBirth { get; set; }
	    }
	}

SampleWcfService

	namespace Services
	{
	    // NOTE: You can use the "Rename" command on the "Refactor" menu to change the class name "SampleWcfService" in both code and config file together.
	    public class SampleWcfService : ISampleWcfService
	    {
	        public Student GetStudent()
	        {
	            return new Student()
	            {
	                Name = "Jenny",
	                Age = 22,
	                DateOfBirth = new DateTime(1995, 3, 7)
	            };
	        }
	
	    }
	}

### Add console hosting ###
Create a console application > add reference to services project

modify App.config

	<configuration>
	  ...
	  <system.serviceModel>
	    <behaviors>
	      <serviceBehaviors>
	        <behavior name="RestServiceBehavior" >
	          <serviceMetadata httpGetEnabled="true" httpsGetEnabled="true"  />
	          <serviceDebug includeExceptionDetailInFaults="false"/>
	        </behavior>
	      </serviceBehaviors>
	      <endpointBehaviors>
	        <behavior name="WebEndpointBehavior">
	          <webHttp />
	        </behavior>
	      </endpointBehaviors>
	    </behaviors>
	    
	    <services>
	      <service behaviorConfiguration="RestServiceBehavior" name="Services.SampleWcfService">
	        <endpoint address="" binding="basicHttpBinding"  contract="Services.ISampleWcfService" />	        
	        <!--wsdl-->
	        <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
	        <endpoint address="mex" binding="mexTcpBinding" contract="IMetadataExchange" />
	        <host>
	          <baseAddresses>
	            <add baseAddress="http://localhost:7000"/>
	            <add baseAddress="net.tcp://localhost:7100"/>
	          </baseAddresses>
	        </host>
	      </service>
	    </services>	    
	    <serviceHostingEnvironment aspNetCompatibilityEnabled="true"
	  multipleSiteBindingsEnabled="true" />
	  </system.serviceModel>
	</configuration>

Please note we add two endpoints to this servcie. It represents we can reference to the project via two endpoints: http and tcp

Modify program.cs

	class Program
    {
        static void Main(string[] args)
        {
            using (var host = new ServiceHost(typeof(SampleWcfService)))
            {
                host.Open();
                Console.WriteLine("host started");
                Console.ReadLine();
            }
        }
    }

### add web hosting ###
Please note that for web hosting WCF service, the <service>.svc file is required. Therefore, we have to create an empty WCF service to generate a svc, then, use the svc file to reference existing services project.

#### way1: use svc file as a shell ####
create a new WCF service > name `WebHost` > **a svc file** and **an associated codebehind cs file** will be generated > remove the codebehind cs file

modify the svc file > change the Service parameter value to the existing WCF service implementation

	<%@ ServiceHost Language="C#" Debug="true" Service="Services.SampleWcfService" CodeBehind="WebHost.svc.cs" %>

![](/images/posts/20171221-wcf-service-13.png)

modify web.config

		...
		<system.serviceModel>
	    <services>
	      <service name="Services.SampleWcfService" behaviorConfiguration="RestServiceBehavior">
	        <endpoint address="" binding="webHttpBinding" contract="Services.ISampleWcfService" behaviorConfiguration="WebEndpointBehavior">
	        </endpoint>
	        <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />         
	     </service>
	      <service name="WebHosting.WebHost2" behaviorConfiguration="RestServiceBehavior">
	        <endpoint address="" binding="webHttpBinding" contract="Services.ISampleWcfService" behaviorConfiguration="WebEndpointBehavior"/>
	        <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
	        <host>
	          <baseAddresses>
	            <add baseAddress="http://localhost:7000" />
	          </baseAddresses>
	        </host>
	      </service>
	    </services>
	
	    <behaviors>
	      <serviceBehaviors>
	        <behavior name="RestServiceBehavior">
	          <serviceMetadata httpGetEnabled="true" httpsGetEnabled="true" />
	          <serviceDebug includeExceptionDetailInFaults="true" />
	        </behavior>
	        <behavior name="">
	          <serviceMetadata httpGetEnabled="true" httpsGetEnabled="true" />
	          <serviceDebug includeExceptionDetailInFaults="true" />
	        </behavior>
	      </serviceBehaviors>
	      <endpointBehaviors>
	        <behavior name="WebEndpointBehavior">
	          <webHttp />
	        </behavior>
	      </endpointBehaviors>
	    </behaviors>
	    
	    <serviceHostingEnvironment aspNetCompatibilityEnabled="true"
	      multipleSiteBindingsEnabled="true" />    
	    
	  </system.serviceModel>
	
	</configuration>

try it

![](/images/posts/20171221-wcf-service-14.png)

![](/images/posts/20171221-wcf-service-15.png)

#### way2: use svc file as a proxy ####
create a new WCF service > name `WebHost2` > **a svc file** and **an associated codebehind cs file** will be generated

modify the codebehind cs file > directly inherit the existing WCF service implementation

WebHost2.svc

	<%@ ServiceHost Language="C#" Debug="true" Service="WebHosting.WebHost2" CodeBehind="WebHost2.svc.cs" %>

WebHost2.svc.cs

	namespace WebHosting
	{
	    //[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
	    public class WebHost2 : SampleWcfService, ISampleWcfService
	    {
	        // directly inherit from the existing service class
	    }
	}

or encapsulte the existing service class

WebHost2.svc.cs

    public class WebHost2 : ISampleWcfService
    {
        private SampleWcfService service;
        public WebHost2()
        {
            this.service = new SampleWcfService();
        }
        public Student GetStudent()
        {
            return this.service.GetStudent();
        }
    }

try the service

![](/images/posts/20171221-wcf-service-16.png)

![](/images/posts/20171221-wcf-service-17.png)

### console WCF service ###
#### way1: check tutorial 1 ####

#### way2: use WCF test client ####
use visual studio built-in tools: WCF test client

![](/images/posts/20171221-wcf-service-11.png)

## WCF service vs. ASMX Web Service ##

**Attributes**
> WCF service is defined by [ServiceContract] and [OperationContract] attributes
> ASMX web service is defined by [WebService] and [WebMethod] attributes.

**Protocols**
> WCF supports a range of protocols, i.e., HTTP, Named Pipes, TCP, and MSMQ
> ASMX web service only supports HTTP protocol.  It is based on SOAP and return data in XML form. It can be consumed by any client that understands xml.

**Hosting** 
> WCF hosting could be implemented via various activation mechanism
> 
> - IIS (Internet Information Service)
> - WAS (Windows Activation Service)
> - Console Application
> - Self-hosting
> - Windows Service
> 
> ASMX web service can be hosted only in IIS

**Security**
> ASMX Security is limited. Normally authentication and authorization is done using IIS and ASP.NET security configuration and transport layer security.For message layer security, WSE can be used. web service only supports security services.
> WCF provides a consistent security programming model for any protocol and it supports many of the same capabilities as IIS and WS-* security protocols, additionally, it provides support for claim-based authorization that provides finer-grained control over resources than role-based security. WCF supports a robust security, trustworthy messaging, transaction and interoperability.

**Serializer**
> WCF Supports DataContractSerializer by employing System.Runtime.Serialization, the performance is better
> ASMX web service supports XmlSerializer in System.Xml.Serialization. XmlSerializer also has restrictions in serializing .NET types to xml:
> 
> - Only public fields or properties of the .NET types can be translated to Xml.
> - Only the classes that implement IEnumerable can be translated.
> - Classes that implement IDictionary, such as Hashtable cannot be serialized. 

**Tools**
> ServiceMetadata tool (svcutil.exe) is used for client generation for a WCF service
> WSDL.EXE tool is used for generating the same for a web service.

**Exception Handling**
> In WCF, unhandled exceptions are handled in a better way by making use of FaultContract. They do not return to the client like in a web service as SOAP faults.

**Bindings**
> WCF supports several types of bindings like BasicHttpBinding, WSDualHttpBinding, WSHttpBinding, etc.
> web service supports only SOAP or XML.

**Multithreading**
> WCF supports multithreading by using the ServiceBehavior Class, whereas this is not supported in a web service.

**Duplex Service Operations**
> WCF supports duplex service operations apart from supporting one-way and request-response service operations, whereas a web service does not support duplex service operations.

What ASMX can do are also can be done by WCF. WCF will replace ASMX. 

## FAQ ##
1. Visual studio complains "Unhandled Exception: System.ServiceModel.AddressAccessDeniedException: HTTP could not register URL  Your process does not have access rights to this namespace (see http://go.microsoft.com/fwlink/?LinkId=70353 for details). ---> System.Net.HttpListenerException: Access is denied"
> it is because of privilidge, please restart visual studio as administrator

1. IIS cannot provides .NET 4.5 services
> Make sure that you have set your application-site version from v2.0 to v4.0 in IIS Manager:
> Application Pools > Your Application > Advanced Settings > .NET Framework Version
> After that, install your ASP.NET.
> For 32-Bit OS (Windows):
> C:\Windows\Microsoft.NET\Framework\v4.0.30319\aspnet_regiis.exe -i
> For 64-Bit OS (Windows):
> C:\Windows\Microsoft.NET\Framework64\v4.0.30319\aspnet_regiis.exe -i
> Restart your application-site in IIS Manager and enjoy.

1. cannot start tcp/ip service
> control panel > programs and features > turn windows feature on > install wcf libs
![](/images/posts/20171221-wcf-service-8.png)

1. iis reports: Could not load type 'System.ServiceModel.Activation.HttpModule' from assembly 'System.ServiceModel...'
> This error can occur when there are multiple versions of the .NET Framework on the computer that is running IIS, and IIS was installed after .NET Framework 4.0 or before the Service Model in Windows Communication Foundation was registered.
> Consider the following scenario. You install the .NET Framework 4.0 such as visual studio 2010+. Then, you install an earlier version of the .NET Framework, or you enable .NET 3.0 WCF HTTP Activation. In this scenario, you may receive this error message
> 
> To resolve this issue, cmd > aspnet_regiis.exe /iru
> 
> The Aspnet_regiis.exe file can be found in one of the following locations:
> 
> - %windir%\Microsoft.NET\Framework\v4.0.30319
> - %windir%\Microsoft.NET\Framework64\v4.0.30319 (on a 64-bit computer)
> 
> then, cmd > iisreset
> 
> ![](/images/posts/20171221-wcf-service-9.png)


## Reference ##
[https://msdn.microsoft.com/en-us/library/bb386386.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/en-us/library/bb386386.aspx?f=255&MSPPError=-2147217396)