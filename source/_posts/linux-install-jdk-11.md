---
title: Ubuntu里安装Java JDK 11
date: 2020-4-3 12:00:00
categories: 
  - 技术
tags: 
  - Linux基础
---

sudo mkdir -p /var/cache/oracle-jdk11-installer-local/

sudo cp jdk-11.0.5_linux-x64_bin.tar.gz /var/cache/oracle-jdk11-installer-local/

sudo add-apt-repository ppa:linuxuprising/java

sudo apt-get update

sudo apt-get install oracle-java11-installer-local