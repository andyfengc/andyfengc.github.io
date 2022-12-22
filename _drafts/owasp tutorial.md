---
layout: post
title: OWASP Top 10 tutorial
author: Andy Feng
---

# Introduction
OWASP(Open Web Application Security Project) is a non-profit organization. It dedicates to enhancing software security. OWASP offers a variety of tools, projects for developers.

OWASP Top 10 is an online publication that ranks top 10 most critical web application security vulnerabilities. 

# OWASP Top 10
## SQL Injection
Ihis attack inject SQL, NonSQL and LDAP data into application. It can be done through the application's input form as SQL queries. If SQL Injection is successful, database's sensitive data may be exposed.

## Broken authentication
Attackers impersonate a valid user online. Session management and credential management are two typical locations where vulnerability happens. 

## Exposed sensitive data
information disclosure or leakage. such as financial info, login info, health info, commercial info, technical info

## XXE injection
XML external entity injection(XXE) is a flaw that a malicious person get access to an application that process XML data. Then, move on to access files on the drive.

## Access Control Issues

## Misconfiguration of Security

## Cross-Site Scripting
Cross-Site Scripting(XSS) is a client-side code injection. Attacker attempts to inject malicious script into a trustworthy website. The script is usually in format of javascript. An attacker can use this flaw to steal cookie and user sessions, obtaining unauthorized access to the system. 

## Unsafe Design

## Using Vulnerability-Known Components

## Inadequate Logging and Monitoring

# References
[Pricing](https://developer.twitter.com/en/pricing.html)

[https://www.codeproject.com/Tips/1076400/Twitter-API-for-beginners](https://www.codeproject.com/Tips/1076400/Twitter-API-for-beginners)