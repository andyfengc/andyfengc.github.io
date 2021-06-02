---
layout: post
title: WebSocket tutorial (1) - node.js, Angular
author: Andy Feng
---

# ws 
`ws` is a library used to build websocket server in node.js. Browser doesn't support to be websocket server, so `ws` cannot run in browser.

1. install lib: 

	`npm install ws`

	`npm install --save @types/ws`


A client library that loads on the browser side socket.io-client

1. write client connect to websocket server

		let ws = new WebSocket('wss://echo.websocket.org');
		    ws.on('open', function(){
		      ws.send('I am Andy');
		      console.log('connected');
		    });
		    ws.on('message', function(data){
		      console.log(data);
		    })

	Please note `ws` library does not work in the browser. The client of `ws` is the back end of nodejs as a client in the WebSocket communication. Browser clients must use the native WebSocket object. 

# Socket.IO is composed of two parts:
A server that integrates with (or mounts on) the Node.JS HTTP Server socket.io

# References #
[ws: a Node.js WebSocket library](https://www.npmjs.com/package/ws)

[https://github.com/websockets/ws](https://github.com/websockets/ws)

[https://socket.io/get-started/chat/](https://socket.io/get-started/chat/)