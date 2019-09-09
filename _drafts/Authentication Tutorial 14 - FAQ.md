---
layout: post
title: ASP.NET Identity authentication 14 - FAQ
author: Andy Feng
---

# Owin vs. Katana vs. OAuth 2.0 vs. ASP.NET Identity vs. OpenID Connect vs. IdentityServer#
`OWIN` is not only a specification and it defines a standard interface between .NET web servers and web applications. The goal of the OWIN interface is to decouple server and application, encourage the development of simple modules for .NET web development, and, by being an open standard, stimulate the open source ecosystem of .NET web development tools.

`Katana` is OWIN implementations for Microsoft servers and frameworks. We can develop our own implementation of Owin too. 

`OAuth 2.0` is a an Authorization protocol. It defines some basic conceptions of Authorization Server, Resource Server, Resource Owner and a few authorization workflows. The purpose of OAuth 2.0 is to enable a third-party app to obtain limited access to an HTTP service. Instead of using the resource owner's credentials to access a protected resource, the client obtains an access token (which is a string denoting a specific scope, lifetime, and other access attributes). Access tokens are issued to third-party clients by an authorization server with the approval of the resource owner. Then, the 3rd-party client can access resource on behalf of resource owner.

`ASP.NET Identity` is a security framework(implementation & libs) and it provides functionality to save and retrieve user's data from a data source. It also provides you with the ability to associate claims and roles to the users, other "login providers" (that would be the case when you "login with facebook" and your user_id from facebook gets associated with your local user id, this information is stored in the AspNetUserLogins table). VS has scaffolding to create identity framework when enable authentication. There is `OAuth` authentication interface in Owin, `ASP.NET Identity` has implementation for that.

`OpenID Connect` is a specification. It is kind of a “super-set” of OAuth 2.0 and always recommended against using OAuth. In constrast to OAuth 2.0, OpenID Connect also defines more standards for Discovery (Webfinger), Dynamic Client Registration (so you don’t have to ask every website for a client id and password manually…), and session management (logout). In actual development, OpenID Connect specification is preferred and it has more than OAuth 2.0. There are multiple implementations of OpenID Connect, e.g. IdentityServer in .NET.

`IdentityServer` is a .NET implementation of `OpenID Connect`.

- In practice, we can use `IdentityServer` or `ASP.NET Identity` to develop authentication feature. 

# References #
