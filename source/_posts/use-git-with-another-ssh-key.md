---
title: Git使用另外的密钥
date: 2023-1-16 12:00:00
categories: 
  - 技术
tags: 
  - git
---

### Git Clone

`git clone git@github.com:<your repo>.git --config core.sshCommand="ssh -i ~/.ssh/<your another key>"`

### Git其他相关命令
在git仓库文件夹下执行

`git config core.sshCommand "ssh -i ~/.ssh/<your another key>"`