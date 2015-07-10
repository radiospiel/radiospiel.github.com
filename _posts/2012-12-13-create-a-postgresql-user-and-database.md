---
layout: post
title: Create a Postgresql user and database
published: true
date: 2012-12-13
tags:
- sql
---

Just a reminder: this is how to create a Postgresql user and database:

```sql
postgres@exs:/root$ sudo su -c psql - postgres 
psql (9.1.4)
Type "help" for help.

postgres=# CREATE USER dontpanic WITH PASSWORD 'PASSWORD';
CREATE ROLE
postgres=# CREATE DATABASE dontpanic;
CREATE DATABASE
postgres=# GRANT ALL PRIVILEGES ON DATABASE dontpanic TO dontpanic;
GRANT
postgres=# \q
