---
layout: post
title: Akka.Net Tutorial
author: Andy Feng
---

# Introduction #
Akka.NET is an open-source library. Akka.NET introduces `Actor` model to make it easier to write concurrent, parallel and distributed systems. 

# Why need Actor model?
Object-oriented programming (OOP) is a popular programming model. One of its conception is encapsulation. Encapsulation protects the internal data of an object and prevents accessible directly from the outside; it can only be modified by invoking a set of predefined methods.

Here is a typical sequence diagram showing the interactions of method calls:

![](/images/posts/20221208-akka.net-1.png)

Th execution is essentially single thread:

![](/images/posts/20221208-akka.net-2.png)

However, the scenario will become complex when it comes to multi thread.

![](/images/posts/20221208-akka.net-3.png)
> In this case, two threads enter the same method() of the same object. This method() might modify the internal state and causes data inconsistent issue. 
> Lock could be a solution but it limits concurrency.

# Actor Model
- Actor communicate with asynchronous messaging instead of method calls
- Actor manage their own state
- When responding to a message, Actor can:

		Create other (child) actors
		Send messages to other actors
		Stop (child) actors or themselves


# References
[https://getakka.net/articles/intro/what-problems-does-actor-model-solve.html](https://getakka.net/articles/intro/what-problems-does-actor-model-solve.html)

[https://medium.com/@abrandaol/first-steps-with-akka-net-38806b2e025b](https://medium.com/@abrandaol/first-steps-with-akka-net-38806b2e025b)