---
title: dubbo异常过滤
date: 2019-02-26 12:23:05
categories:
  - 分布式
tags:
  - 分布式
  - rpc
---
dubbo的自定义异常处理和过滤器使用
<!-- more -->

## com.alibaba.dubbo.rpc.filter.ExceptionFilter

1. checked异常
2. 在方法签名中的异常
3. 与api接口同jar包的异常
4. jdk异常
5. dubbo的异常

出现以上情况的异常，直接返回result，在客户端会出现对应的异常。  
如果不是以上异常，dubbo服务端则会将异常包装为一个RuntimeException抛给客户端。
