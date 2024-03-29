---
title: SSH端口转发
date: 2019-1-29 12:00:00
categories: 
  - 技术
tags: 
  - Linux进阶
---

### 简介
SSH端口转发也被称作SSH隧道(SSH Tunnel)，因为它们都是通过SSH登陆之后，在SSH客户端与SSH服务端之间建立了一个隧道，从而进行通信
SSH隧道是安全的，因为SSH是通过加密传输数据的(SSH全称为Secure Shell)

### 作用
* 加密SSH client 与 SSH server 传输的数据
* 突破防火墙限制

### 类型
* 动态端口转发
* 本地端口转发
* 远程端口转发

**动态端口转发**
将本地不加密的网络连接，全部改走SSH连接，并通过代理服务器发送
`ssh -D 1080 user@host`
SSH会建立一个socket，监听本地的1080端口
传向本地1080端口数据，通过SSH连接传送到SSH服务端，再由__SSH服务端__发送出去，相当于将SSH服务端当成代理服务器
**本地端口转发**
将发送到本机端口的请求，通过SSH服务端，转发到目标端口
`ssh -L 8080:192.168.1.1:80 user@host`
传向本地8080端口数据，通过SSH连接传送到SSH服务端，再由__SSH服务端__发送到__服务端__的192.168.1.1的80端口
**远程端口转发**
将发送到远程端口的请求，通过SSH客户端，转发到目标端口
`ssh -R 8080:192.168.1.1:80 user@host`
传向host的8080端口数据，通过SSH连接传送到SSH客户端，再由__SSH客户端__发送到__客户端__的192.168.1.1的80端口

### 链式转发
只需要一台具有公网IP的机器，两个独立网络可以互通

```
假设A主机在公司，B主机在家，C主机为远程云主机
如何实现在B主机上访问A主机的服务
```

### 附：
#### 常用命令
```plain
ssh -ND 1080 user@host
ssh -NfL 8080:localhost:80 user@host
ssh -NfR 8080:localhost:80 user@host
```
#### SSH命令参数
```plain
-C：压缩数据传输
-N:不执行脚本或命令，通常与-f连用
-f：后台认证用户/密码，通常与-N连用，不用登陆到远程主机
-g：在-L/-D/-R参数中，允许远程主机连接到建立的转发端口，如果不加这个参数，只允许本地主机建立连接
-i：从指定文件中读取传输时使用的密钥文件
-D：指定一个本地机器的端口，建立SSH隧道，把通过隧道到数据转发到远端的所有地址
-L：将本地机(客户机)的某个端口转发到远端指定机器的指定端口
-R：将远程主机(服务器)的某个端口转发到本地端指定机器的指定端口
```

### 参考文档
> [实战 SSH 端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/index.html)
> [SSH 端口转发](https://www.cnblogs.com/sparkdev/p/7497388.html)

