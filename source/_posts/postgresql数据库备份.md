---
title: postgresql数据库备份
tags: postgresql
categories: 数据库
translate_title: postgresql-database-backup
date: 2019-01-23 15:29:26
---

**postgresql pg_dump dump数据库表 命令**
```sql
pg_dump -U sa(用户名) -d abase(数据库名) -f kettle.sql（文件名，默认会保存的postgre sql bin 目录下）。

pg_dump -h [db ip] -U [db user name] -s [db name] -n [schema name] > [file path]

pg_dump 指定模式的表和数据
pg_dump -h 133.3.0.4  -U sa -d abase -n db_sjzx> sjzx20170916.sql -p 5433
```