---
layout: post
title: SOA, web services, microservices
author: Andy Feng
---

## What is the difference ##
both webservices and microservices are part of SOA architecture

soa is an architecture to build application. It is a global concept represents build application via a collection of services. some implementation:
- corba
- web services
- ActiveMQ, JMS
- wcf

soa is independent of vendors, products, technologies, 3 major components:
- service provider
- service registry
- service consumer

webservice is a technology for providing services specifically through web or http. It is used to construct applications over internet. 
- usually in http format
- each service provides apis
- the communication carries json/xml data
- each service can be built via different technologies

microservice is also an architecture, it could be regarded as a subset of soa because it is focus on small services. Specifically, microservice:
- small, independent, deployable unit
- focus on smaller business domain
- can be used to construct loosely coupled apps
- Communicate with other microservices.
- Runs in its own process.
- Integrates via well-known interfaces.
- Owns its own data storage.
- soa usually refers to large block of deployed services

microservice could be implemented as docker in .net
 

## What is docker ##
docker is open source container-based technology. container is a component which packages all an application with all of its dependencies. It is an isolated environment, and the application has all it needs to run inside the container. 
Essentially, it creates a snapshot of our environment.

- write our apps
- write dockfile
- compile and built image
- build container from image and run app

## References ##
[https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/)