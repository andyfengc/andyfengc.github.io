---
layout: post
title: ASP.NET Identity authentication 13 - IdentityServer fundamentals
author: Andy Feng
---

# Introduction
Here is a typical architecture of modern applications:

![](/images/posts/20191217-identity-server-2.png)

For security purpose, each and every layer (front-end, middle-tier and back-end) has to protect resources and implement authentication and/or authorization – often against the same user store. i.e.

![](/images/posts/20191217-identity-server-1.png)

However, we shouldn't duplicate authentication logic in each layer. We should introduce an authentication service to verify permission across those applications and endpoints. 

> Different literature uses different terms for the authentication service - you probably also find security token service, identity provider, authorization server, IP-STS and more.

The most common authentication protocols are SAML2p, WS-Federation and OpenID Connect. 

- SAML2p being the most popular and the most widely deployed.
- OpenID Connect is the newest of the three, but is considered to be the future because it has the most potential for modern applications. It was built for mobile application scenarios right from the start and is designed to be API friendly. 

## Review of Oauth 2.0 and OpenID Connect
`OAuth2` is a protocol that allows applications to request access tokens from a security token service and use them to communicate with APIs. This delegation reduces complexity in both the client applications as well as the APIs since authentication and authorization can be centralized.

`OpenID Connect` and `OAuth 2.0` are very similar – in fact OpenID Connect is an extension on top of OAuth 2.0. The two fundamental security concerns, authentication and API access, are combined into a single protocol - often with a single round trip to the security token service.

The combination of OpenID Connect and OAuth 2.0 might be the best approach to secure modern applications for the foreseeable future. `IdentityServer`(now v4) is an implementation of these two protocols and is highly optimized to solve the typical security problems of today’s mobile, native and web applications.

# IdentityServer
IdentityServer is an OpenID Connect provider - it implements the OpenID Connect and OAuth 2.0 protocols. IdentityServer plays as a middleware that adds the spec compliant OpenID Connect and OAuth 2.0 endpoints to an arbitrary ASP.NET Core application.

Typically, we build (or re-use) an application that contains a login and logout page, and the `IdentityServer` middleware adds the necessary protocol heads to it, so that client applications can talk to the backend Identity Server via middleware using those standard protocols.

From technical perspective:

![](/images/posts/20191217-identity-server-3.png)

From user view:

![](/images/posts/20191217-identity-server-4.png)

IdentityServer has a number of jobs and features - including:

- protect your resources
- authenticate users using a local account store or via an external identity provider
- provide session management and single sign-on
- manage and authenticate clients
- issue identity and access tokens to clients
- validate tokens

# Outline
`Microsoft.Owin.Security.OpenIdConnect`

certmgr.msc

# References
[https://identityserver.github.io/Documentation/docsv2/overview/mvcGettingStarted.html](https://identityserver.github.io/Documentation/docsv2/overview/mvcGettingStarted.html)

to read

http://docs.identityserver.io/en/latest/intro/terminology.html

http://docs.identityserver.io/en/latest/topics/startup.html#

https://manfredsteyer.github.io/angular-oauth2-oidc/docs/additional-documentation/refreshing-a-token.html
