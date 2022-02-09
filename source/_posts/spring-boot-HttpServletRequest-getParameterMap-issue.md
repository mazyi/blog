---
title: spring boot中request的getParameterMap坑
date: 2022-02-09 20:00:00
categories: 
  - 技术
tags: 
  - spring boot
---

### 简介
使用spring boot 的时候，通常会拦截请求中的参数。在使用Filter拦截Request的时候，除了要注意body是一个stream，读取了一次要重新设置一个stream以外，鲜有人提到拦截前后getParameterMap方法的行为会产生变化

### 问题重现

这是Controller的代码，获取到parameterMap后返回。
![Controller代码](/images/controller.png)

#### 正常情况
在Filter中构造的Request不获取请求的body，也不重新设置body的stream
![不获取请求body](/images/not-get-body.png)

我们在Controller中调用getParameterMap得到请求参数，将会是如下结果：
1. url里的参数获取正常
2. form里的参数获取正常
![不获取请求cody的返回](/images/not-get-body-response.png)

#### 异常情况
在Filter中构造的Request中获取请求的body，重新设置body的stream，在获取body前**不调用getParameterMap方法**
![获取请求body但不调用getParameterMap](/images/get-body-not-call-fun.png)

我们在Controller中调用getParameterMap得到请求参数，将会是如下结果：
1. url里的参数获取正常
2. **form里的参数获取不正常**
![不获取请求cody的返回](/images/get-body-not-call-fun-response.png)

但是如果在**获取body前调用getParameterMap方法**
![获取请求body且调用getParameterMap](/images/get-body-call-fun.png)
将会是如下结果：
1. url里的参数获取正常
2. **form里的参数获取正常**
![不获取请求cody的返回](/images/get-body-call-fun-response.png)

### 原因探讨