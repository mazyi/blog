---
title: 断路器
date: 2019-6-12 12:00:00
categories: 
  - 技术
tags: 
  - 分布式基础
---

### 简介
断路器是保障服务稳定的一个方式

### 介绍
断路器又称熔断器，作用是暂时切断服务的调用。主要场景是当上游服务发现下游服务不可用时，为避免血崩效应，主动切断对下游服务的调用，当服务恢复后再重新调用。

### 工作原理
- 断路
  - 当请求后端服务失败数量超过一定比例时，断路器将切换到开路状态，所有请求均将失败
  - 一段时间后自动切换到半开路状态，此时将放行一个请求并且判断是否成功
  - 放行的一个请求成功断路器切换到闭路状态，否则切换到开路状态
- 降级（Fallback）
  - 当请求失败时，可以指定一个默认的返回值，这样请求不至于异常
- 资源隔离
  - 调用不同的下游服务使用不同的线程池，会增加系统额外开销，但是可以保障不同的服务不因为断路而互相产生影响

### 实现与原理
Java中通常有实现了的库，常见的有Hystrix、Resilience4j、Sentinel，这里不做具体如何使用的介绍

- Hystrix内部使用了10个Bucket，每秒产生1个新的Bucket来聚合这一秒的请求状态
- Resilience4j内部是使用了RingBuffer来记录每次请求的状态
- Sentinel与Hystrix类似使用了滑动窗口聚合的方式记录请求的状态

### 参考文档
> [熔断器Hystrix](http://www.ityouknow.com/springcloud/2017/05/16/springcloud-hystrix.html)
> [Sentinel 与 Hystrix 的对比](https://github.com/alibaba/Sentinel/wiki/Sentinel-%E4%B8%8E-Hystrix-%E7%9A%84%E5%AF%B9%E6%AF%94)