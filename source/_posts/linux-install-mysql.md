---
title: Ubuntu里安装MySQL数据库
date: 2020-4-4 20:00:00
categories: 
  - 技术
tags: 
  - MySQL
  - Linux基础
---

```
sudo apt-get install mysql-server mysql-client

cat /etc/mysql/debian.cnf 找到初始帐号密码

mysql -u username -p password 用初始帐号密码登陆mysql

1. use mysql;
2. update mysql.user set authentication_string=password('yourpassword') where user='root' and Host ='localhost';
3. update user set plugin="mysql_native_password";
4. flush privileges;
5. quit; 

sudo service mysql restart
```