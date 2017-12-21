---
layout: post
title: ASP.NET WCF Service Tutorial 
author: Andy Feng
---

## Introduction ##
WCF integrate Web Services and .NET routing technologies

## Create a WCF service ##
Create a web project > right click > new item > wcf service

![](/images/posts/20171221-wcf-service-1.png)

A WCF contains one or more interfaces and its implemented classes. two files will be created: 

interface: ISampleWcfService.cs

	[ServiceContract]
	public interface ISampleWcfService
	{
	    [OperationContract]
	    void DoWork();
	}

implementation class: SampleWcfService.svc

    public class SampleWcfService : ISampleWcfService
    {
        public void DoWork()
        {
        }
    }

Add a new method and we got

ISampleWcfService.cs

	[ServiceContract]
    public interface ISampleWcfService
    {
        [OperationContract]
        void DoWork();

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
        public void DoWork()
        {
        }

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

solution explorer > select SampleWcfService.svc > ctrl + f5, run

![](/images/posts/20171221-wcf-service-2.png)

double click an endpoint > click `Invoke` button

![](/images/posts/20171221-wcf-service-3.png)

We can also view this WCF service via link:

![](/images/posts/20171221-wcf-service-5.png)

### Add GET, JSON ###
add attribute to endpoint method

	[ServiceContract]
    public interface ISampleWcfService
    {
        [OperationContract]
        void DoWork();

        [WebGet(UriTemplate = "students", ResponseFormat = WebMessageFormat.Json)]
        //[WebInvoke(Method = "GET", ResponseFormat = WebMessageFormat.Json, UriTemplate = "students")] // equavalent
        [OperationContract]
        Student GetStudent();
    }

Then we access `GET http://localhost:60000/Wcf/SampleWcfService.svc/students`

![](/images/posts/20171221-wcf-service-7.png)

### Add IIS hosting and enable RESTFUL WCF services ###
modify web.config

	  <system.serviceModel>
	    <services>
	      <service name="WebApplication1.Wcf.SampleWcfService" behaviorConfiguration="RestServiceBehavior">
	        <endpoint address="" binding="webHttpBinding" contract="WebApplication1.Wcf.ISampleWcfService" />
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
	        <behavior>
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


## Consume WCF service ##
Once a service is hosted, we can consume it in client applications.

First, we need to create a proxy for the service. This proxy is used by the client application to interact with the service. 

### way1 to create proxy class ###

project name > services reference > right click > add service reference > ok

![](/images/posts/20171221-wcf-service-4.png)

here we got the proxy class

![](/images/posts/20171221-wcf-service-6.png)

### way2 to create proxy class ###
Alternatively, we can use service utility in Visual Studiuo command prompt. We can create the proxy class and its configuration information via: 

	svcutil http://localhost:60000/Wcf/SampleWcfService.svc

After executing this command, we will get two files generated in the default location.

MyService.cs − Proxy class for the WCF service
output.config − Configuration information about the service

### Create a client application ###
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

## WCF service vs. ASMX Web Service ##

Attributes
> WCF service is defined by [ServiceContract] and [OperationContract] attributes
> ASMX web service is defined by [WebService] and [WebMethod] attributes.

Protocols
> WCF supports a range of protocols, i.e., HTTP, Named Pipes, TCP, and MSMQ
> ASMX web service only supports HTTP protocol.

Hosting 
> WCF hosting could be implemented via various activation mechanism
> 
> - IIS (Internet Information Service)
> - WAS (Windows Activation Service)
> - Console Application
> - Self-hosting
> - Windows Service
> 
> ASMX web service can be hosted only in IIS

Security
> ASMX Security is limited. Normally authentication and authorization is done using IIS and ASP.NET security configuration and transport layer security.For message layer security, WSE can be used.
> WCF provides a consistent security programming model for any protocol and it supports many of the same capabilities as IIS and WS-* security protocols, additionally, it provides support for claim-based authorization that provides finer-grained control over resources than role-based security.WCF security is consistent regardless of the host that is used to implement WCF service.

Services − WCF supports a robust security, trustworthy messaging, transaction and interoperability, while a web service only supports security services.

Serializer
> WCF Supports DataContractSerializer by employing System.Runtime.Serialization, the performance is better
> ASMX web service supports XmlSerializer in System.Xml.Serialization. XmlSerializer also has restrictions in serializing .NET types to xml:
> 
> - Only public fields or properties of the .NET types can be translated to Xml.
> - Only the classes that implement IEnumerable can be translated.
> - Classes that implement IDictionary, such as Hashtable cannot be serialized. 

Tools
> ServiceMetadata tool (svcutil.exe) is used for client generation for a WCF service
> WSDL.EXE tool is used for generating the same for a web service.

Exception Handling − In WCF, unhandled exceptions are handled in a better way by making use of FaultContract. They do not return to the client like in a web service as SOAP faults.

Bindings − WCF supports several types of bindings like BasicHttpBinding, WSDualHttpBinding, WSHttpBinding, etc., while a web service supports only SOAP or XML.

Multithreading − WCF supports multithreading by using the ServiceBehavior Class, whereas this is not supported in a web service.

Duplex Service Operations −	WCF supports duplex service operations apart from supporting one-way and request-response service operations, whereas a web service does not support duplex service operations.

What ASMX can do are also can be done by WCF. WCF will replace ASMX. 

## Reference ##
[https://msdn.microsoft.com/en-us/library/bb386386.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/en-us/library/bb386386.aspx?f=255&MSPPError=-2147217396)