title: mysql8 远程连接解决办法
author: OdinSam
tags:
  - MySql
categories:
  - MySql
abbrlink: 8b92
date: 2022-06-30 13:37:00
---
```sql
grant all privileges on *.* to 'root'@'%' ;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
flush privileges;
```