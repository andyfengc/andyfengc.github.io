---
layout: post
title: WebSocket tutorial (1) - Fundamentals
author: Andy Feng
---

# Introduction
`WebSocket` is one of duplex communication techniques between the server and clients. It is defined as a two-way communication between the servers and the clients, which mean both the parties communicate and exchange data at the same time.

The key points of Web Sockets are true concurrency and optimization of performance, resulting in more responsive and rich web applications.

# Common duplex communication techniques
There are multiple duplex communication techniques between the server and the client.

- `Polling`: Polling can be defined as a method in the client. The client makes periodic requests in a specified time interval to the Server synchronously. The response of the server includes available data or some warning message in it. Typically, we use a hidden iframe to refresh data periodically from the server.

- `Long Polling`: Similar to polling, the client and the server keep the connection active until some data is fetched or timeout occurs. If the connection is lost due to some reasons, the client can start over and perform sequential request. Long polling is nothing but performance improvement over polling process, but constant requests may slow down the process.

- `Streaming`: Streaming can be considered as the best option for real-time data transmission. The server keeps the connection open and active with the client until and unless the required data is being fetched. In this case, the connection is said to be open indefinitely. Streaming includes HTTP headers which increases the file size, increasing delay. This can be considered as a major drawback.

- `Postback and AJAX`: `AJAX(Asynchronous Javascript and XML)` launched at 2005 is based on Javascript's `XmlHttpRequest` Object. `XmlHttpRequest` Object allows execution of the Javascript without reloading the complete web page. `AJAX` sends and receives only a portion of the web page.

	Code snippet of AJAX call with XmlHttpRequest Object is as follows −

		var xhttp;
		
		if (window.XMLHttpRequest) {
		   xhttp = new XMLHttpRequest();
		} else {
		   // code for IE6, IE5
		   xhttp = new ActiveXObject("Microsoft.XMLHTTP");
		}

	The major drawbacks of AJAX in comparison with Web Sockets are −
	
	- They send HTTP headers, which makes total size larger.
	- The communication is half-duplex.
	- The web server consumes more resources.

- `HTML5`
	
	HTML5 is a robust framework for developing and designing web applications. The main pillars include Mark-up, CSS3 and Javascript APIs together. HTML5 using HTTP protocal and doesn't support built-in duplex communication. Instead, there are some technique such as `cross frame communication` and `HTTP polling` to partially implement duplex communication.

# Web Socket Protocol
A WebSocket is a persistent connection between a client and server. WebSockets provide a bidirectional, full-duplex communications channel that operates over `HTTP` through a single `TCP/IP` socket connection. 

The main features of web sockets are as follows:

- Web socket protocol is being standardized, which means real time communication between web servers and clients is possible with the help of this protocol.
- Web sockets are transforming to cross platform standard for real time communication between a client and the server.
- This standard enables new kind of the applications. Businesses for real time web application can speed up with the help of this technology.
- The biggest advantage of Web Socket is it provides a two-way communication (full duplex) over a single TCP connection.
- Web Socket is an independent TCP-based protocol, but it is designed to support any other protocol that would traditionally run only on top of a pure TCP connection.
	- Web Socket is a transport layer on top of which any other protocol can run. The Web Socket API supports the ability to define sub-protocols: protocol libraries that can interpret specific protocols.
	- Examples of such protocols include XMPP, STOMP, and AMQP. The developers no longer have to think in terms of the HTTP request-response paradigm.
	- The only requirement on the browser-side is to run a JavaScript library that can interpret the Web Socket handshake, establish and maintain a Web Socket connection.
	- On the server side, the industry standard is to use existing protocol libraries that run on top of TCP and leverage a Web Socket Gateway.
- Reduce unnecessary network traffic and latency using full duplex through a single connection (instead of two).Web Socket connections are faster than HTTP.
- Streaming through proxies and firewalls, with the support of upstream and downstream communication simultaneously.

Web socket protocol defined a schema in its URL pattern.

![](/images/posts/20190617-websocket-1.jpg)

WebSocket URIs use a new scheme `ws:` or `wss:` (for a secure WebSocket). The remainder of the URI is the same as an HTTP URI: a host, port, path and any query parameters.

	"ws:" "//" host [ ":" port ] path [ "?" query ]
	"wss:" "//" host [ ":" port ] path [ "?" query ]

WebSockets begin life as a standard HTTP request and response. Within that request response chain, the client asks to open a WebSocket connection, and the server responds (if its able to). If this initial handshake is successful, the client and server have agreed to use the existing TCP/IP connection that was established for the HTTP request as a WebSocket connection. Data can now flow over this connection using a basic framed message protocol. Once both parties acknowledge that the WebSocket connection should be closed, the TCP connection is torn down.

HTTP vs. Websocket

![](/images/posts/20200909-websocket-1.png)

1. WebSocket connections are established by upgrading an HTTP request/response pair. Web Socket connections are initiated via HTTP and HTTP typically interpret Web Socket handshakes as an Upgrade request. A client wants to establish a Websockets connection will send an HTTP request that includes a few required headers. e.g.

		GET ws://example.com:8181/ HTTP/1.1
		Host: localhost:8181
		Pragma: no-cache
		Cache-Control: no-cache

		Connection: Upgrade
		Upgrade: websocket
		Sec-WebSocket-Version: 13
		Sec-WebSocket-Key: q4xkcO32u266gldTuKaSOw==

1. Once a client sends the initial request to open a WebSocket connection, it waits for the server’s reply. 

	Response

		HTTP/1.1 101 Switching Protocols
		Upgrade: websocket
		Connection: Upgrade
		Sec-WebSocket-Accept: fA9dggdnMPU79lJgAE3W4TRnyDM=

1. After the client receives the server response, the WebSocket connection is open to start transmitting data. The handshake is established.

	In Websocket protocol, message is divided into a number of discrete chunks. Chunk data like:

		  0                   1                   2                   3
		 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
		+-+-+-+-+-------+-+-------------+-------------------------------+
		|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
		|I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
		|N|V|V|V|       |S|             |   (if payload len==126/127)   |
		| |1|2|3|       |K|             |                               |
		+-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
		|     Extended payload length continued, if payload len == 127  |
		+ - - - - - - - - - - - - - - - +-------------------------------+
		|                               |Masking-key, if MASK set to 1  |
		+-------------------------------+-------------------------------+
		| Masking-key (continued)       |          Payload Data         |
		+-------------------------------- - - - - - - - - - - - - - - - +
		:                     Payload Data continued ...                :
		+ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
		|                     Payload Data continued ...                |
		+---------------------------------------------------------------+

1. To close a WebSocket connection, a closing frame is sent (opcode 0x08) and the close frame may contain a body that indicates the reason for closing. If either side of a connection receives a close frame, it must send a close frame in response, and no more data should be sent over the connection. Once the close frame has been received by both parties, the TCP connection is torn down. Please note that the server always initiates closing the TCP connection.

# Steps
1. initialize the connection from client to the server for communication between them. 

	var socket = new WebSocket('wss://echo.websocket.org');

	> The URL mentioned above is a public address that can be used for testing and experiments. The websocket.org server is always up and when it receives the message and sends it back to the client.

1. There are four main Web Socket API events. Each of the events are handled by implementing the functions like onopen, onmessage, onclose and onerror functions respectively. In Javascript, they can be implemented with the help of addEventListener method.

	- Open
	
		the open event is fired as the initial handshake between client and server. The event, which is raised once the connection is established, is called onopen.

	- Message

		Message event happens usually when the server sends some data. Messages sent by the server to the client can include plain text messages, binary data or images. Whenever the data is sent, the onmessage function is fired.

	- Close

		Close event marks the end of the communication between server and the client. After marking the end of communication with the help of onclose event, no messages can be further transferred between the server and the client. Closing the event can happen due to poor connectivity as well.

	- Error

		Error marks for some mistake, which happens during the communication. Onerror is always followed by termination of connection. The detailed description of each and every event is discussed in further chapters.

1. The Web Socket protocol supports two main actions

	- send()

		This action is usually preferred for some communication with the server, which includes sending messages, which includes text files, binary data or images. ending the messages is only possible if the connection is open.

			// Send the data
			socket.send('some text...');

	- close()

		This method stands for goodbye handshake. It terminates the connection completely and no data can be transferred until the connection is re-established.

			// Close the connection if open
			if (socket.readyState === WebSocket.OPEN){
			  socket.close( );
			}

		It is also possible to close the connection deliberately

			socket.close(1000,”Deliberate Connection”);

# Demo
## Websocket client using Javascript
Create a websocket and register event handlers

	var websocket = new WebSocket('wss://echo.websocket.org');
	websocket.onopen = function(evt) { console.log('connected'); };
	websocket.onclose = function(evt) { console.log('closed'); };
	websocket.onmessage = function(evt) { console.log(evt.data); };
	websocket.onerror = function(evt) { console.log('error'); };

test

	websocket.send('some text...');

screenshot

![](/images/posts/20200909-websocket-2.png)

To check whether we are offline, we can use

	websocket.onclose = function(event) { 
		// Connection closed.
		// Firstly, check the reason.
	
		if (event.code != 1000) {
		  // Error code 1000 means that the connection was closed normally.
		  // Try to reconnect.		
		  if (!navigator.onLine) {
		     alert("You are offline. Please connect to the Internet and try again.");
		  }
		} 
	};

## WebSocket server using c#.
Because websocket is duplex communcation, websocket server has 4 major methods similar to websocket client.

[https://github.com/statianzo/Fleck](https://github.com/statianzo/Fleck)

[https://www.tutorialspoint.com/websockets/websockets_server_working.htm](https://www.tutorialspoint.com/websockets/websockets_server_working.htm)

## Server Side Implementations
Node.js

	Socket.IO
	WebSocket-Node
	ws

Java

	Jetty

Ruby

	EventMachine

Python

	pywebsocket
	Tornado

Erlang

	Shirasu

C++

	libwebsockets

.NET, C#

	[SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr)
	SuperWebSocket

Angular

	[https://github.com/aspnet/SignalR](https://github.com/aspnet/SignalR)

## Websocket client using Angular
[https://blog.angulartraining.com/how-to-use-websockets-with-rxjs-and-angular-b98e7fd8be82](https://blog.angulartraining.com/how-to-use-websockets-with-rxjs-and-angular-b98e7fd8be82)
[https://tutorialedge.net/typescript/angular/angular-websockets-tutorial/](https://tutorialedge.net/typescript/angular/angular-websockets-tutorial/)

# FAQ
## Websocket vs. HTTP
[https://www.tutorialspoint.com/websockets/websockets_api.htm](https://www.tutorialspoint.com/websockets/websockets_api.htm)

Web Socket is a stateful protocol whereas HTTP is a stateless protocol. Web Socket connections can scale vertically on a single server whereas HTTP can scale horizontally. There are some proprietary solutions for Web Socket horizontal scaling, but they are not based on standards. HTTP comes with a lot of other goodies such as caching, routing, and multiplexing. All of these need to be defined on top of Web Socket.

## Cross-Origin Communication
Being a modern protocol, cross-origin communication is baked right into WebSocket. WebSocket enables communication between parties on any domain. The server decides whether to make its service available to all clients or only those that reside on a set of well-defined domains.

## Proxy Servers
Every new technology comes with a new set of problems. In the case of WebSocket it is the compatibility with proxy servers, which mediate HTTP connections in most company networks. The WebSocket protocol uses the HTTP upgrade system (which is normally used for HTTP/SSL) to "upgrade" an HTTP connection to a WebSocket connection. Some proxy servers do not like this and will drop the connection. Thus, even if a given client uses the WebSocket protocol, it may not be possible to establish a connection. This makes the next section even more important :)

## The Server Side
Using WebSocket creates a whole new usage pattern for server side applications. While traditional server stacks such as LAMP are designed around the HTTP request/response cycle they often do not deal well with a large number of open WebSocket connections. Keeping a large number of connections open at the same time requires an architecture that receives high concurrency at a low performance cost.

## Webhooks vs. Websockets
[https://stackoverflow.com/questions/23172760/differences-between-webhook-and-websocket](https://stackoverflow.com/questions/23172760/differences-between-webhook-and-websocket)

# References #
[WebSockets Tutorial](https://www.tutorialspoint.com/websockets/index.htm)

[How Do Websockets Work?](https://sookocheff.com/post/networking/how-do-websockets-work/)

[Echo Test](https://www.websocket.org/echo.html)

[Introducing WebSockets: Bringing Sockets to the Web](https://www.html5rocks.com/en/tutorials/websockets/basics/)

[to read: Real-Time Web Apps Made Easy with WebSockets in .NET 4.5](https://www.codemag.com/Article/1210051/Real-Time-Web-Apps-Made-Easy-with-WebSockets-in-.NET-4.5)
