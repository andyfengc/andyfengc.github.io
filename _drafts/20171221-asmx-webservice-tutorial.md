---
layout: post
title: ASP.NET SOAP asmx Web Services Tutorial 
author: Andy Feng
---

## Create a webservice ##
First, create a web project

### enable routing ###
1. webforms project

2. webapi/mvc project

	need to enable router for asmx, modify App_Start\RouteConfig.cs

	public class RouteConfig
    {
        public static void RegisterRoutes(RouteCollection routes)
        {
            routes.IgnoreRoute("{*x}", new { x = @".*\.asmx(/.*)?" }); 
			...
        }
    }

### Create a asmx webservice ###

right click project > new item > asmx webservice

![](/images/posts/20171221-new-asmx-webservice.png)

Here is the code:

	namespace WebApplication1.WebServices
	{
	    /// <summary>
	    /// Summary description for SampleWebService
	    /// </summary>
	    [WebService(Namespace = "http://tempuri.org/")]
	    [WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
	    [System.ComponentModel.ToolboxItem(false)]
	    // To allow this Web Service to be called from script, using ASP.NET AJAX, uncomment the following line. 
	    // [System.Web.Script.Services.ScriptService]
	    public class SampleWebService : System.Web.Services.WebService
	    {
	
	        [WebMethod]
	        public string HelloWorld()
	        {
	            return "Hello World";
	        }
	    }
	}

1. inherit `System.Web.Services.WebService` is optional, but it provides session/application state management, caching features
1. Namespace is used to diffentiate other webservice with ours
1. WebServiceBining is used to validate the specification of this webservice, we could disable it by set ConformsTo = WsiProfiles.None
1. Attribute [WebService] for class and [WebMethod] for method is required

### Create a new method ###

Create a new method in the webservice, decorate with [WebMethod], returns an object

	[WebService(Namespace = "http://tempuri.org/")]
    [WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
    [System.ComponentModel.ToolboxItem(false)]
    // To allow this Web Service to be called from script, using ASP.NET AJAX, uncomment the following line. 
    // [System.Web.Script.Services.ScriptService]
    public class SampleWebService : System.Web.Services.WebService
    {

        [WebMethod]
        public string HelloWorld()
        {
            return "Hello World";
        }

        [WebMethod]
        [ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]
        public Student GetStudent()
        {
            Context.Response.Clear();
            Context.Response.ContentType = "application/json";
            return new Student()
            {
                Name = "andy",
                Age = 25,
                DateOfBirth = new DateTime(1993, 5, 12)
            };
        }
    }

    public class Student
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public DateTime DateOfBirth { get; set; }
    }

ctrl + f5, run

![](/images/posts/20171221-asmx-webservice-1.png)

it lists current endpoints, we can also add `?wsdl` to get wsdl document

![](/images/posts/20171221-asmx-webservice-6.png)

click an endpoint

![](/images/posts/20171221-asmx-webservice-2.png)

here is the description, click Invoke to make a mock call

![](/images/posts/20171221-asmx-webservice-3.png)

### Allow GET, Add JSON ###
asmx uses SOAP protocol and by default it is POST request.

modify web.config

	  <system.web>    
	    ...
	    <webServices>
	      <protocols>
	        <add name="HttpGet"/>
	        <add name="HttpPost"/>
	      </protocols>
	    </webServices>
	    
	  </system.web>

add new attribute before the method:

	[ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]

we can get result use GET request:

![](/images/posts/20171221-asmx-webservice-4.png)

By default, the result is XML format, we can add `Content-Type=application/json` in the header to get json result

![](/images/posts/20171221-asmx-webservice-5.png)

By default, json result is included in d property

### Improve endpoint: enable session state ###
If we need session management, cache feature, pls remember our webservice class has to inherit `System.Web.Services.WebService` class

in order to let the server maintains the session state, we just modify the attribute [WebMethod] to `[WebMethod(EnableSession =true)]`

Server will keep the session state for this endpoint

In the code, we call global Session object to get the saved state. Session object is used like a dictionary.

	WebMethod(EnableSession =true)]
    [ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]
    public Student GetStudent()
    {

        Student s = (Student) Session["student"];
       	...
    }

### Improve endpoint 2 ###

1. add cache to endpoint

		[WebMethod(EnableSession =true, Description ="this endpoint returns a student", CacheDuration = 60)]
	    [ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]
	    public Student GetStudent()
		{...}

	here CacheDuration is seconds

1. add buffer to endpoint

	    [WebMethod(EnableSession =true, Description ="this endpoint returns a student", CacheDuration = 60, BufferResponse = true)]
	    [ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]
	    public Student GetStudent()
		{...}

1. add alias to endpoint if we have overloading endpoints

		[WebMethod(EnableSession =true, MessageName = "GetStudentNewName")]
        [ScriptMethod(UseHttpGet = true, ResponseFormat = ResponseFormat.Json)]
        public Student GetStudent()
		{...}

1. enable ajax call

	uncomment attribute 

		[System.Web.Script.Services.ScriptService]
		public Student GetStudent()
		{...}

## Consume a webservice ##
The client could be web application(web forms, mvc), console, windows form or any other applications by other languages.

Here is an example for .NET project (web form, console, windows form)

### Create proxy class ###

right click Services Reference > add service reference

![](/images/posts/20171221-asmx-webservice-7.png)

Enter webservice wsdl file, click go. Or we can click discover to let it find all webservices in current project.

![](/images/posts/20171221-asmx-webservice-8.png)

Select SOAP > ok, the proxy class will be created

![](/images/posts/20171221-asmx-webservice-9.png)

### Create a client ###
Here, we create a web form to demostrate

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
	            var client = new SampleWebServiceSoapClient("SampleWebServiceSoap12");
	            var student = client.GetStudent();
	            lblName.Text = student.Name;
	            lblAge.Text = student.Age.ToString();
	            lblDob.Text = student.DateOfBirth.ToString("yyyy-MM-dd");
	        }
	    }

It looks like

![](/images/posts/20171221-asmx-webservice-10.png)

Please note that when we click the `Load` button, it creates new a proxy instance and help us make the SOAP call. Finally, when we click the button in the page, we got

![](/images/posts/20171221-asmx-webservice-11.png)

## Authentication ##
 todo...