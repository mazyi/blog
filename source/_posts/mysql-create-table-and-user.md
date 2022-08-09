---
title: MySQL里创建表和用户
date: 2022-8-9 20:00:00
categories: 
  - 技术
tags: 
  - MySQL
---

create database TABLE_NAME default character set utf8mb4 collate utf8mb4_general_ci;

CREATE USER 'USER_NAME'@'%' IDENTIFIED BY 'USER_PASSWORD';

GRANT ALL ON TABLE_NAME.* TO 'USER_NAME'@'%';