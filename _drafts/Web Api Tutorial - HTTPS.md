---
layout: post
title: Web Api tutorial - HTTPS
author: Andy Feng
---

# Transport protocol #
HTTPS = HTTP + TLS(SSL)

X.509 certificate is a file that contains information about the owner of the certicate and who issues or created the certificate. It also contains additional information about how long it is valid, public key and so on.

A certificate is typically issued to a web server and the web server then hosts the certificate for the public. The web server also keeps a separate private key paring with certificate. 

![](/images/posts/20180515-certificate-1.png)

For each request, client encrypt messages using certificate, then the server decrypt messages using private key.

# OWIN #