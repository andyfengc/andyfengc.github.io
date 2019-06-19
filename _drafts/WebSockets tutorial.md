---
layout: post
title: WebSockets tutorial
author: Andy Feng
---

# Introduction
`Web Sockets` is one of duplex communication techniques between the server and clients. It is defined as a two-way communication between the servers and the clients, which mean both the parties communicate and exchange data at the same time.

The key points of Web Sockets are true concurrency and optimization of performance, resulting in more responsive and rich web applications.

## Web Socket Protocol ##
Web Socket Protocol defines a full duplex communication from the ground up. It represents an evolution, which was awaited for a long time in client/server web technology. The main features of web sockets are as follows:

- Web socket protocol is being standardized, which means real time communication between web servers and clients is possible with the help of this protocol.

- Web sockets are transforming to cross platform standard for real time communication between a client and the server.

- This standard enables new kind of the applications. Businesses for real time web application can speed up with the help of this technology.

- The biggest advantage of Web Socket is it provides a two-way communication (full duplex) over a single TCP connection.

## URL ##

Similar to `HTTP` which has its own set of schemas such as `http` and `https`, Web socket protocol defined a schema in its URL pattern.

![](/images/posts/20190617-websocket-1.jpg)

## Duplex communication techniques
There are multiple duplex communication techniques between the server and the client.

- `Polling`: Polling can be defined as a method in the client. The client makes periodic requests in a specified time interval to the Server synchronously. The response of the server includes available data or some warning message in it. Typically, we use a hidden iframe to refresh data periodically from the server.

- `Long Polling`: Similar to polling, the client and the server keep the connection active until some data is fetched or timeout occurs. If the connection is lost due to some reasons, the client can start over and perform sequential request. Long polling is nothing but performance improvement over polling process, but constant requests may slow down the process.

- `Streaming`: Streaming can be considered as the best option for real-time data transmission. The server keeps the connection open and active with the client until and unless the required data is being fetched. In this case, the connection is said to be open indefinitely. Streaming includes HTTP headers which increases the file size, increasing delay. This can be considered as a major drawback.

- `Postback and AJAX`: `AJAX(Asynchronous Javascript and XML)` is based on Javascript's `XmlHttpRequest` Object. `XmlHttpRequest` Object allows execution of the Javascript without reloading the complete web page. `AJAX` sends and receives only a portion of the web page.

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

# References #
https://www.tutorialspoint.com/websockets/websockets_overview.htm