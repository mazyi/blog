---
title: SSH时配置文件的加载
date: 2019-3-1 12:00:00
categories: 
  - 技术
tags: 
  - Linux进阶
---

#### 简介
SSH可以在两台主机之间建立可靠安全的连接，并可以执行命令，但是不同的建立连接的方式Linux会有不同的处理方式

#### bash的四种类型
* interactive + login shell
* non-interactive + login shell
* interactive + non-login shell
* non-interactive + non-login shell
#### interactive + login shell
##### 常见例子
- 用户直接登陆到机器获得的第一个shell
- 用户使用ssh user@remote获得的shell
##### 加载的配置文件
首先加载`/etc/profile`文件，然后再尝试依次去加载下列三个配置文件之一，一旦找到其中一个便不再接着寻找：
`~/.bash_profile`、`~/.bash_login`、`~/.profile`
#### non-interactive + login shell
##### 常见例子
- bash -l script.sh，-l参数是将shell作为一个login shell启动，而执行脚本又使它为non-interactive shell
##### 加载的配置文件
与第一种相同
#### interactive + non-login shell
##### 常见例子
- 在一个已有shell中运行bash，此时会打开一个交互式的shell，且不再需要登陆
##### 加载的配置文件
加载`/etc/bash.bashrc`和`~/.bashrc`文件
#### non-interactive + non-login shell
##### 常见例子
- bash script.sh
- ssh user@remote command
##### 加载的配置文件
- 寻找环境变量`BASH_ENV`，将变量的值作为文件名进行查找，如果找到便加载它
- 加载`~/.bashrc`文件，默认文件会判断，如果进入shell时没有`-i`参数则什么都不做

#### 对比
```
+----------------+--------+-----------+---------------+
|                | login  |interactive|non-interactive|
|                |        |non-login  |non-login      |
+----------------+--------+-----------+---------------+
|/etc/profile    |   A    |           |               |
+----------------+--------+-----------+---------------+
|/etc/bash.bashrc|        |    A      |               |
+----------------+--------+-----------+---------------+
|~/.bashrc       |        |    B      |       a       |
+----------------+--------+-----------+---------------+
|~/.bash_profile |   B1   |           |               |
+----------------+--------+-----------+---------------+
|~/.bash_login   |   B2   |           |               |
+----------------+--------+-----------+---------------+
|~/.profile      |   B3   |           |               |
+----------------+--------+-----------+---------------+
|BASH_ENV        |        |           |       A       |
+----------------+--------+-----------+---------------+
```

#### 最佳实践
- `~/.bash_profile`：应该尽可能的简单，放置bash的专属命令，通常会在最后加载`.profile`和`.bashrc`(注意顺序)
- `~/.bash_login`：别用它，如果已经创建，创建`.bash_profile`来屏蔽它被调用
- `~/.profile`：此文件用于login shell，所有想在整个用户会话期间都有效的内容都应该放置于此，比如启动进程，环境变量等
- `~/.bashrc`：只放置与bash有关的命令，所有与交互有关的命令都应该出现在此，比如bash的补全、alias、颜色、提示符等等

#### 参考文档
> [ssh连接远程主机执行脚本的环境变量问题](http://feihu.me/blog/2014/env-problem-when-ssh-executing-command-on-remote)
