---
layout: post
title: Authentication Tutorial (1) Introduction
author: Andy Feng
---

# Authentication vs. Authorization #
Authentication is the verification of the credentials of the connection attempt. This process consists of sending the credentials from the remote access client to the remote access server in an either plaintext or encrypted form by using an authentication protocol.

Authorization is the verification that the connection attempt is allowed. Authorization occurs after successful authentication.

To summarize, Authentication is stating that you are who are you are and Authorization is asking if you have access to a certain resource.

# Basic Authentication
The most simple way to deal with authentication is to use HTTP basic authentication. We use a special HTTP header where we add 'username:password' encoded in base64.

	GET / HTTP/1.1
	Host: www.service.com
	Authorization: Basic am9obnNtaXRoOjEyMzQ1Ng==

Simulate basic authentication in PostMan

![](/images/posts/20181120-auth-1.png)

![](/images/posts/20181120-auth-2.png)

Note that even though our credentials are encoded, they are not encrypted! It is very easy to retrieve the username and password from a basic authentication. Do not use this authentication scheme on plain HTTP, but only through SSL/TLS.

The downsides of basic authentication is that we need to send over the password on every request. Also, it does not safeguard against tampering of headers or body.

# HMAC (hash based message authentication)
Instead of having passwords that need to be sent over each request, we can send a hashed version of the password, together with more information. 

Let's assume we wanna access below protected resource:

	url: GET /users/johnsmith/account
	username: johnsmith
	user secret: 123456
	additional info: i.e. the current timestamp, a random number, or the md5 of the message body in order to prevent tampering of the body

Next, we generate a hmac:

	digest = base64encode(hmac("sha256", "123456", "GET+/users/johnsmith/account"))

or calculate with current time and a nonce

	digest = base64encode(hmac("sha256", "123456", "GET+/users/johnsmith/account+20apr201312:59:24+noncexxx"))

This digest we can send over as a HTTP header:

	GET /users/johnsmith/account HTTP/1.1
	Host: service.com
	Authentication: hmac johnsmith:[digest]
	Date: 20 apr 2018 12:59:24
	Nonce(optional): nonce123

Please note that the server can got almost all the details to calculate `digest` from the request including url, username, nounce and etc. except secret. Actually, the server already saved all secrets of users, which are not encrypted on the server. Therefore, the server already has all info to calculate digest. Then, the server can generate the digest.

After the server reconstruct the digest, it compares the calculated digest against the passing digest from client. If it is the same, the server make further process. Otherwise, it represents the request was tempered during pass over to the server and the server will reject the request.

# OAuth

# References
[https://blog.restcase.com/restful-api-authentication-basics/](https://blog.restcase.com/restful-api-authentication-basics/)
