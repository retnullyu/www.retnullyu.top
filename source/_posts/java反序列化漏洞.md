---
title: java反序列化漏洞
date: 2020-10-14 14:29:17
tags: [java反序列化漏洞]
categories: java反序列化漏洞
---

### java 反序列化

> 利用objectoutputstream的的writeObject方法实现序列化，用objectinputstream的readobject实现反序列化
>
> 只有实现了java.io.serializable接口才可被序列化

### 修复

> 利用Objectinputstream的resolveclass方法检测类名
>
> 多为利用重写方法的方式直接重写相关类名检测方法

> RMI（*Remote Method Invocation*）远程方法调用：是 Java 的一组拥护开发分布式应用程序的 API，实现了不同操作系统之间程序的方法调用。值得注意的是，RMI 的传输 100% 基于反序列化，Java RMI 的默认端口是 1099 端口。
>
> JMX：JMX 是一套标准的代理和服务，用户可以在任何 Java 应用程序中使用这些代理和服务实现管理,中间件软件 WebLogic 的管理页面就是基于 JMX 开发的，而 JBoss 则整个系统都基于 JMX 构架。 
>
> *JNDI (Java Naming and Directory Interface) 是一个应用程序设计的 API，为开发人员提供了查找和访问各种命名和目录服务的通用、统一的接口。*
>
> > 简单来说，JNDI (Java Naming and Directory Interface) 是一组应用程序接口，它为开发人员查找和访问各种资源提供了统一的通用接口，可以用来定位用户、网络、机器、对象和服务等各种资源。比如可以利用JNDI在局域网上定位一台打印机，也可以用JNDI来定位数据库服务或一个远程Java对象。JNDI底层支持RMI远程对象，RMI注册的服务可以通过JNDI接口来访问和调用。