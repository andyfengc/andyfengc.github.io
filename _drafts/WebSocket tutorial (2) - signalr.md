---
layout: post
title: WebSocket tutorial (1) - dotnet
author: Andy Feng
---

# built-in library
connect echo websocket server:

	using (var ws = new ClientWebSocket())
	    try
	    {
	        await ws.ConnectAsync(new Uri("wss://echo.websocket.org"), CancellationToken.None);
	        var source = new CancellationTokenSource();
	        source.CancelAfter(5000);
	
	        var iterationNo = 0;
	        // restricted to 5 iteration only
	        while (ws.State == WebSocketState.Open && iterationNo++ < 5)
	        {
	            string msg = "andy say hello at: " + DateTime.Now;
	            ArraySegment<byte> bytesToSend = new ArraySegment<byte>(Encoding.UTF8.GetBytes(msg));
	            await ws.SendAsync(bytesToSend, WebSocketMessageType.Text, true, source.Token);
	            //Receive buffer
	            var receiveBuffer = new byte[1024];
	            //Multipacket response
	            var offset = 0;
	            var dataPerPacket = 10; //Just for example
	            while (true)
	            {
	                ArraySegment<byte> bytesReceived = new ArraySegment<byte>(receiveBuffer, offset, dataPerPacket);
	                WebSocketReceiveResult result = await ws.ReceiveAsync(bytesReceived, source.Token);
	                //Partial data received
	                Console.WriteLine("Data:{0}", Encoding.UTF8.GetString(receiveBuffer, offset, result.Count));
	                offset += result.Count;
	                if (result.EndOfMessage)
	                    break;
	            }
	            Console.WriteLine("Complete response: {0}", Encoding.UTF8.GetString(receiveBuffer, 0, offset));
	        }
	
	
	    }
	    catch (Exception ex)
	    {
	        Console.WriteLine($"ERROR - {ex.Message}");
	    }
	}

![](/images/posts/20200923-websocket-1.png)

connect trinngo stock websocket server:

	using (var ws = new ClientWebSocket())
	{
	    try
	    {
	        await ws.ConnectAsync(new Uri("wss://api.tiingo.com/test"), CancellationToken.None);
	        var source = new CancellationTokenSource();
	        source.CancelAfter(5000);
	
	        var subscribe = @" {
	        ""eventName"":""subscribe"",
	        ""eventData"": {
	                ""authToken"": ""api-key""
	                    }
	        }";
	
	        if (ws.State == WebSocketState.Open)
	        {
	            ArraySegment<byte> bytesToSend = new ArraySegment<byte>(Encoding.UTF8.GetBytes(subscribe));
	            await ws.SendAsync(bytesToSend, WebSocketMessageType.Text, true, source.Token);
	            //Receive buffer
	            var receiveBuffer = new byte[1024];
	            //Multipacket response
	            var offset = 0;
	            while (true)
	            {
	                ArraySegment<byte> bytesReceived = new ArraySegment<byte>(receiveBuffer, offset, receiveBuffer.Length);
	                WebSocketReceiveResult result = await ws.ReceiveAsync(bytesReceived, source.Token);
	                //Partial data received
	                Console.WriteLine("Data:{0}", Encoding.UTF8.GetString(receiveBuffer, offset, result.Count));
	                offset += result.Count;
	                if (result.EndOfMessage)
	                    break;
	            }
	            Console.WriteLine("Complete response: {0}", Encoding.UTF8.GetString(receiveBuffer, 0, offset));
	        }
	    }
	    catch (Exception ex)
	    {
	        Console.WriteLine($"ERROR - {ex.Message}");
	    }
	}

As you can see, this code is low-level. There are some i.e. 3rd party library wrappers in .NET; also has advanced Websocket frameworks such as SingalR.

# websocket-client
[websocket-client](https://github.com/Marfusios/websocket-client) is a wrapper over native C# class `ClientWebSocket` with built-in reconnection and error handling. It supports .net core.

Install `Websocket.Client` via nuget

![](/images/posts/20200923-websocket-2.png)

connect trinngo stock websocket server:

	var exitEvent = new ManualResetEvent(false);
	var url = new Uri("wss://api.tiingo.com/test");
	
	using (var client = new WebsocketClient(url))
	{
	    client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
	    client.Start();
	
	    Task.Run(() => client.Send(@"
	        {
	    ""eventName"":""subscribe"",
	    ""eventData"": {
	            ""authToken"": ""api-key""
	                }
	    }
	        "));
	    exitEvent.WaitOne();
	}

![](/images/posts/20200923-websocket-3.png)

# SignalR
ASP.NET SignalR is a library for ASP.NET developers that simplifies the process of adding real-time web functionality to applications. Real-time web functionality is the ability to have server code push content to connected clients instantly as it becomes available, rather than having the server wait for a client to request new data.

SignalR API contains two models for communicating between clients and servers: Persistent Connections and Hubs.

![](/images/posts/20200909-websocket-3.png)

1. `Connection` represents a simple endpoint for sending single-recipient, grouped, or broadcast messages. The Persistent Connection API (represented in .NET code by the PersistentConnection class) gives the developer direct access to the low-level communication protocol that SignalR exposes. Using the Connections communication model will be familiar to developers who have used connection-based APIs such as Windows Communication Foundation.

1. `Hub` is a more high-level pipeline built upon the Connection API that allows your client and server to call methods on each other directly. SignalR handles the dispatching across machine boundaries as if by magic, allowing clients to call methods on the server as easily as local methods, and vice versa. Using the Hubs communication model will be familiar to developers who have used remote invocation APIs such as .NET Remoting. Using a Hub also allows you to pass strongly typed parameters to methods, enabling model binding.

# SignalR demo using .NET framework
1. Install lib via nuget: `Microsoft.AspNet.SignalR`
	
	![](/images/posts/20200910-signalr-1.png)

1. Create a new class `ChatHub`

		using System;
		using System.Web;
		using Microsoft.AspNet.SignalR;
		namespace SignalRChat
		{
		    public class ChatHub : Hub
		    {
		        public void Send(string name, string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(name, message);
		        }
		    }
		}

1. see below tutorial:

	[https://www.codeproject.com/Articles/1218390/Implement-a-Websocket-API-with-Owin-and-SignalR-Pa](https://www.codeproject.com/Articles/1218390/Implement-a-Websocket-API-with-Owin-and-SignalR-Pa)

	[https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr)

	[https://www.codeproject.com/Articles/1188400/Beginners-Guide-to-Using-SignalR-via-ASP-NET](https://www.codeproject.com/Articles/1188400/Beginners-Guide-to-Using-SignalR-via-ASP-NET)

	[https://www.c-sharpcorner.com/article/understanding-signalr-from-scratch/](https://www.c-sharpcorner.com/article/understanding-signalr-from-scratch/)

	[https://docs.microsoft.com/en-us/aspnet/signalr/overview/guide-to-the-api/hubs-api-guide-server#hubnames](https://docs.microsoft.com/en-us/aspnet/signalr/overview/guide-to-the-api/hubs-api-guide-server#hubnames)

it seems that Signalr .NET framework not support websocket url like wss:xxx or ws:xxx. We have to use jquery-signalr.js lib to access backend SignalR

SignalR .net core allows connecting to the server with bare webSocket and support url.

# SignalR demo using .NET Core
## Create SignalR server and client
1. SignalR already built-in in .net core. no need to install lib

1. Create a new .net core web project

	![](/images/posts/20200910-signalr-2.png)

	![](/images/posts/20200910-signalr-3.png)

	![](/images/posts/20200910-signalr-4.png)

1. Install SignalR client lib. Because SignalR framework implement websocket and other long polling technique, it requires microsoft's js implementation.

	Solution Explorer > right-click the project > select Add > Client-Side Library > in the Add Client-Side Library dialog, select unpkg > enter @microsoft/signalr@3, and select the latest version > Select Choose specific files > expand the dist/browser folder, and select signalr.js and signalr.min.js >  select Install.

	![](/images/posts/20200910-signalr-5.png)
	![](/images/posts/20200910-signalr-6.png)
	![](/images/posts/20200910-signalr-7.png)
	![](/images/posts/20200910-signalr-9.png)

1. now the library for server and client is ready and we are ready to build server and client

1. Create a SignalR hub
	
	A hub is a class that serves as a high-level pipeline that handles client-server communication.

	create Hubs folder > create a ChatHub.cs file

		using Microsoft.AspNetCore.SignalR;
		using System.Threading.Tasks;
		
		namespace SignalRChat.Hubs
		{
		    public class ChatHub : Hub
		    {
		        public async Task SendMessage(string user, string message)
		        {
		            await Clients.All.SendAsync("ReceiveMessage", user, message);
		        }
		    }
		}

	The ChatHub class inherits from the SignalR Hub class. The Hub class manages connections, groups, and messaging.

	The SendMessage method can be called by a connected client to send a message to all clients. SignalR code is asynchronous to provide maximum scalability.

1. Configure SignalR > Startup.cs. The SignalR server must be configured to pass SignalR requests to SignalR pipeline

		namespace MvcCore
		{
		    public class Startup
		    {
		        public Startup(IConfiguration configuration)
		        {
		            Configuration = configuration;
		        }
		
		        public IConfiguration Configuration { get; }
		
		        // This method gets called by the runtime. Use this method to add services to the container.
		        public void ConfigureServices(IServiceCollection services)
		        {
		            ...
		            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
		            services.AddSignalR();
		        }
		
		        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
		        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
		        {
		           ...
		            app.UseSignalR(routes =>
		            {
		                routes.MapHub<ChatHub>("/chathub");
		            });
		            app.UseMvc();
		        }
		    }
		}

1. Add SignalR client code > modify Pages\Index.cshtml with the following code:

		@page
		<div class="container">
		    <div class="row">&nbsp;</div>
		    <div class="row">
		        <div class="col-6">&nbsp;</div>
		        <div class="col-6">
		            User..........<input type="text" id="userInput" />
		            <br />
		            Message...<input type="text" id="messageInput" />
		            <input type="button" id="sendButton" value="Send Message" />
		        </div>
		    </div>
		    <div class="row">
		        <div class="col-12">
		            <hr />
		        </div>
		    </div>
		    <div class="row">
		        <div class="col-6">&nbsp;</div>
		        <div class="col-6">
		            <ul id="messagesList"></ul>
		        </div>
		    </div>
		</div>
		<script src="~/lib/@@microsoft/signalr/dist/browser/signalr.js"></script>
		<script src="~/js/chat.js"></script>

	> list with id="messagesList" for displaying messages that are received from the SignalR hub.
	
	wwwroot/js folder > create a chat.js file with the following code:

		"use strict";
		
		var connection = new signalR.HubConnectionBuilder().withUrl("/chatHub").build();
		
		//Disable send button until connection is established
		document.getElementById("sendButton").disabled = true;

		// receive message
		connection.on("ReceiveMessage", function (user, message) {
		    var msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
		    var encodedMsg = user + " says " + msg;
		    var li = document.createElement("li");
		    li.textContent = encodedMsg;
		    document.getElementById("messagesList").appendChild(li);
		});
		
		// connected signalr
		connection.start().then(function(){
		    document.getElementById("sendButton").disabled = false;
		}).catch(function (err) {
		    return console.error(err.toString());
		});

		// send message
		document.getElementById("sendButton").addEventListener("click", function (event) {
		    var user = document.getElementById("userInput").value;
		    var message = document.getElementById("messageInput").value;
		    connection.invoke("SendMessage", user, message).catch(function (err) {
		        return console.error(err.toString());
		    });
		    event.preventDefault();
		});

	the preceding code:
	> Creates and starts a connection.
	> 
	> Adds to the submit button a handler that sends messages to the hub.
	> 
	> Adds to the connection object a handler that receives messages from the hub and adds them to the list.

1. run the app > ctrl + f5

	![](/images/posts/20200910-signalr-8.png)

## client using Angular
1. install microsoft lib: 

	`npm install @aspnet/signalr`

	or
	
	`npm install @microsoft/signalr` (newer)

1. create a new component

	websocket-test.component.ts

		import { Component, OnInit, Injectable } from '@angular/core';
		import { HubConnection, HubConnectionBuilder } from "@microsoft/signalr";
		import { HttpClient, HttpHeaders } from '@angular/common/http';
		import { StockService } from 'src/app/services/stock.service';
		
		@Component({
		  selector: 'app-websocket-test',
		  templateUrl: './websocket-test.component.html',
		  styleUrls: ['./websocket-test.component.scss'],
		  providers: [StockService]
		})
		export class WebsocketTestComponent implements OnInit {
		  hubConnection: HubConnection;
		  public messages: string[] = [];
		  public user: string;
		  public message: string;
		
		  constructor(private http: HttpClient
		    , private stockService: StockService) { }
		
		  ngOnInit(): void {
		    let builder = new HubConnectionBuilder();
		    this.hubConnection = builder.withUrl('http://localhost:50538/chatHub').build();
		    //this.hubConnection = builder.withUrl('https://localhost:44322/chathub').build();
		
		    // handler for message coming from the server to client
		    this.hubConnection.on('ReceiveMessage', (user, message) => {
		      this.messages.push(user + ": " + message);
		    })
		
		    // starting the connection
		    this.hubConnection.start();
		  }
		
		  send() {
		    // message sent from the client to the server
		    this.hubConnection.invoke("SendMessage", this.user, this.message);
		  }
		}

	websocket-test.component.html

		<p>websocket-test works!</p>
		
		<input type="text" [(ngModel)]="user" placeholder="User">
		<input type="text" [(ngModel)]="message" placeholder="Message">
		
		<button (click)="send()">Send</button>
		
		<p *ngFor="let m of messages">{{m}}</p>

1. In .net server, enable CORS > Startup.cs

		public void Configure(IApplicationBuilder app, IHostingEnvironment env)
		{
		    ...
			app.UseCors(builder => builder
                .AllowAnyHeader()
                .AllowAnyMethod()
                .AllowCredentials()
                .SetIsOriginAllowed(host => true)
                //.AllowAnyOrigin() // cannot use together with AllowCredentials(), use WithOrigins("https://example.com") instead
            );
			,,,
		    app.UseSignalR(routes =>
		    {
		        routes.MapHub<ChatHub>("/chathub");
		
		    });
		    app.UseMvc();
		}

	SignalR actually requires AllowCredentials

1. Start .net server, then run the angular client and .NET client

	![](/images/posts/20200910-signalr-13.png)


# FAQ
1. Access to XMLHttpRequest has been blocked by CORS policy: Response to preflight request doesn't pass access control check: Redirect is not allowed for a preflight request.

	![](/images/posts/20200910-signalr-10.png)

	way 1: disable HTTPS redirection, use HTTP 

	Startup.cs

		public void Configure(IApplicationBuilder app, IHostingEnvironment env)
		{
		    ...
		    // app.UseHttpsRedirection(); // disable it
		    ...
		    app.UseMvc();
		
		}

	or
	
	![](/images/posts/20200910-signalr-11.png)

	way2: install Microsoft.AspNetCore.Cors lib. e.g. .net core v2.1.1
	
	![](/images/posts/20200910-signalr-12.png)
	

## SignalR vs. Websocket
ASP.NET Core SignalR is a library that simplifies adding real-time web functionality to apps. SignalR only has websocket implementation but also has more. SignalR uses WebSockets whenever possible; if websocket is not supported, SignalR will fallback other techniques such as server sent event, forever frames and the last choice is long polling.

![](/images/posts/20200909-websocket-4.png)


For most applications, we recommend SignalR over raw WebSockets. SignalR provides transport fallback for environments where WebSockets is not available. It also provides a simple remote procedure call app model. And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.


# References #
[Introduction to SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/introduction-to-signalr)

[Tutorial: Get started with ASP.NET Core SignalR-2.1](https://docs.microsoft.com/en-ca/aspnet/core/tutorials/signalr?tabs=visual-studio&view=aspnetcore-2.1)

[Introduction to ASP.NET Core SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-3.1)

[Tutorial: Get started with ASP.NET Core SignalR-3.1](https://docs.microsoft.com/en-ca/aspnet/core/tutorials/signalr?tabs=visual-studio&view=aspnetcore-3.1)

[WebSockets support in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/websockets?view=aspnetcore-3.1#:~:text=This%20article%20explains%20how%20to,%2C%20dashboard%2C%20and%20game%20apps.)

[Differences between ASP.NET SignalR and ASP.NET Core SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr/version-differences?view=aspnetcore-3.1)

[Build Real-time Applications with ASP.NET Core SignalR](https://www.codemag.com/article/1807061/Build-Real-time-Applications-with-ASP.NET-Core-SignalR)

[Tutorial: Real-time chat with SignalR 2](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr)

[SignalR and Web Sockets](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/signalr-and-web-sockets)

[.NET Core how to add any origin to CORS and allow credentials](https://mykkon.work/how-to-setup-any-origin/)

[fix “The CORS protocol does not allow specifying a wildcard (any) origin and credentials at the same time” error](https://stackoverflow.com/questions/53675850/how-to-fix-the-cors-protocol-does-not-allow-specifying-a-wildcard-any-origin)

[Send message to specific user in SignalR](https://ngohungphuc.wordpress.com/2019/05/01/send-message-to-specific-user-in-signalr/)

[ASP.NET Core SignalR JavaScript client-3.1](https://docs.microsoft.com/en-us/aspnet/core/signalr/javascript-client?view=aspnetcore-3.1)

[Creating Manual Javascript SignalR Client](https://www.c-sharpcorner.com/article/creating-manual-javascript-signalr-client/)

[ASP.NET Core SignalR .NET Client](https://docs.microsoft.com/en-us/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.1&tabs=visual-studio)

[WebSockets support in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/websockets?view=aspnetcore-3.1#sample-app)

[A complete SignalR with ASP Net Core example with WSS, Authentication, Nginx](https://kimsereyblog.blogspot.com/2018/07/signalr-with-asp-net-core.html)

[ASP.NET Core SignalR configuration](https://docs.microsoft.com/en-us/aspnet/core/signalr/configuration?view=aspnetcore-3.1&tabs=dotnet)

[Mapping SignalR Users to Connections](https://docs.microsoft.com/en-us/aspnet/signalr/overview/guide-to-the-api/mapping-users-to-connections)

[https://stackoverflow.com/questions/30523478/connecting-to-websocket-using-c-sharp-i-can-connect-using-javascript-but-c-sha](https://stackoverflow.com/questions/30523478/connecting-to-websocket-using-c-sharp-i-can-connect-using-javascript-but-c-sha)

[ASP.NET SignalR Hubs API Guide - Server (C#), .NET framework](https://docs.microsoft.com/en-us/aspnet/signalr/overview/guide-to-the-api/hubs-api-guide-server#callfromhub)