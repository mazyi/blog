---
title: spring boot中request的getParameterMap坑
date: 2022-02-09 20:00:00
categories: 
  - 技术
tags: 
  - spring boot
---

### 简介
使用spring boot 的时候，通常会拦截请求中的参数在使用Filter拦截Request的时候，除了要注意body是一个stream，读取了一次要重新设置一个stream以外，鲜有人提到拦截前后getParameterMap方法的行为会产生变化

### 问题重现

这是Controller的代码，获取到parameterMap后返回
![Controller代码](/images/controller.png)

#### 正常情况
在Filter中构造的Request不获取请求的body，也不重新设置body的stream
![不获取请求body](/images/not-get-body.png)

我们在Controller中调用getParameterMap()得到请求参数，将会是如下结果：
1. url里的参数获取正常
2. form里的参数获取正常
![不获取请求body的返回](/images/not-get-body-response.png)

#### 异常情况
在Filter中构造的Request中获取请求的body，重新设置body的stream，在获取body前**不调用getParameterMap方法**
![获取请求body但不调用getParameterMap](/images/get-body-not-call-fun.png)

我们在Controller中调用getParameterMap得到请求参数，将会是如下结果：
1. url里的参数获取正常
2. **form里的参数获取不正常**
![获取请求body但不调用getParameterMap的返回](/images/get-body-not-call-fun-response.png)

但是如果在**获取body前调用getParameterMap方法**
![获取请求body且调用getParameterMap](/images/get-body-call-fun.png)
将会是如下结果：
1. url里的参数获取正常
2. **form里的参数获取正常**
![获取请求body且调用getParameterMap的返回](/images/get-body-call-fun-response.png)

### 原因探究

直接在源码上断点，注意有时候反编译的源码会不匹配，在反编译出来的文件上点Download Source Code，会下载真正的源码进而匹配

从getParameterMap()函数看进去的话，经过几个函数，可以在org.apache.catalina.connector.Request类中看到getParameterMap()函数如下：
![getParameterMap()函数](/images/getParameterMap.png)

这里的代码说明，如果获取了一次map，之后就不用再获取了，所以只要第一次获取的是正确的，以后都会是正确的
问题在于，为什么我们不先获取map，直接在controller里面获取就不对了呢？

继续看就会进入Request类的getParameterNames()函数、parseParameters()函数
![parseParameters()函数](/images/parseParameters.png)

3183行的parameters.handleQueryParameters()处理了请求里的参数，此时如果usingInputStream是true，就return了，而3202行才处理了form里的参数
继续追踪usingInputStream的设值，发现只有一个地方，那就是getInputStream()函数！
![设置usingInputStream的getInputStream()函数](/images/usingInputStream.png)

所以一旦在调用getInputStream()前没有调用过getParameterMap()的话，form里的参数就会丢失


### 小结
这个问题是在做支付宝支付回调时，参考支付宝提供的demo里的代码，发现在controller里使用getParameterMap()获取不到参数，结合支付宝文档里说*异步通知 header 是 Content-Type:application/x-www-form-urlencoded; text/html; charset=utf-8*，就开始用form表单测试，最后得出结论，一定要在getInputStrem()前调用一次getParameterMap()