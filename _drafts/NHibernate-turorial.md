---
layout: post
title: NHibernate tutorial
author: Andy Feng
---

based on Hibernate
build on top of ADO.NET


implements a object-relational mapper, 

In object world, we have object-oriented, we have associations between objects, we have references. In relational world, it is set-based, the relationships is based on foreign keys.

Nhibernate helps us solve the mapping. steps:
- define classes, c# code
- mapping meta data, configuration, how to translate from c# class to database tables, many ways, e.g. mapping files, attibutes
- database schema, ddl, tables

difference nhibernate entity framework
both of them sits on top of ado.net, SqlConnection, sqlcommand,DataReader, dataset(datatables, datarows, datacolumns), dataadapter 

what is second level cache hibernate

