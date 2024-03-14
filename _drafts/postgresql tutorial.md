---
layout: post
title: PostgreSQL tutorial
author: Andy Feng
---

# psql
By default, a Postgres user called `postgres` has full superadmin access to entire PostgreSQL instance.

# open psql command line interface in full admin mode.

`sudo -u postgres psql`

# create user

`sudo -u postgres createuser <username>`

`create user <username> with encrypted password '123';`

change password
`alter user <username> with encrypted password '<password>';`

# create database

`sudo -u postgres createdb <dbname>`

# Granting privileges on database

`grant all privileges on database <dbname> to <username> ;`

# Grant select permission of schema to user

GRANT SELECT ON ALL TABLES IN SCHEMA <schemaname> TO <username>;

GRANT USAGE ON SCHEMA <schemaname> TO <username>;

# Revoke permission of schema to user
REVOKE ALL PRIVILEGES ON SCHEMA <schemaname> FROM <username>;

REVOKE ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA <schemaname> FROM <username>;

REVOKE ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA <schemaname> FROM <username>;

REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA <schemaname> FROM <username>;

REVOKE ALL ON ALL TABLES IN SCHEMA <schemaname> FROM <username>;

REVOKE USAGE ON SCHEMA <schemaname> FROM <username>;

# Create a schema, a view and grant select permission
CREATE SCHEMA new_schema;

GRANT USAGE ON new_schema TO restricted_user;

CREATE VIEW new_schema.secret_view AS SELECT * from sec_schema.secret_table;

sec_schema=# GRANT SELECT ON new_schema.secret_view TO restricted_user;

# 
[https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e](https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e)