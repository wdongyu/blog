---
title: Zipkin
date: 2017-09-21 00:47:02
tags:
---

### 导语 ：
> 近日在学习Spring boot项目Spring-petclinic时，其中的Tracing-Server使用了Zipkin追踪系统，故写下此文以记录学习Zipkin的过程。

# 认识Zipkin
![](http://zipkin.io/public/img/zipkin-logo-200x119.jpg)
Zipkin是Twitter基于Google Dapper论文的一个开源项目，是一个分布式追踪系统。在实际微服务架构中，各服务之间在彼此调用时，将特定的追踪消息传送至Zipkin，Zipkin通过收集和管理在微服务架构中的调用延迟数据，将其聚合处理、存储、展示等，达到对服务进行监控追踪的目的，同时用户可通过web UI方便地获得网络延迟、调用链路、系统依赖等等信息。

## Zipkin的优点
* 对应用透明，低侵入
* 低开销，高稳定
* 可扩展

## 组成
Zipkin主要涉及四个组件Collector，Storage，API和web UI
* Collector负责接收从各个服务传送过来的数据
* Storage一方面与数据库进行交互，存储数据，另一方面将数据提供给API接口，供用户及web UI进行查询使用
* API中，可使用Query查询Storage中存储的数据，提供简单的JSON API，同时将返回数据提供给web UI
* web UI负责为用户提供简单的web界面，用户可直观地看出各个服务的信息及时间百分比

## 内部数据模型
![](http://dl2.iteye.com/upload/attachment/0122/3845/f3b9f886-00f1-303f-96f5-73ea0f406f44.png)
服务之间的相互调用通常能够展开成一棵树的形式，在Zipkin中使用基本数据单元**span**来表示追踪树中树节点引用的数据结构体，其中包含的字段有：
#### 基础数据 
* Trace Id：全局追踪Id，一条服务调用链路的唯一标识
* Span Id：局部Id，一次服务调用的标识，理论上在同一个Trace Id下唯一。
* Parent Id：父服务调用Span的标识。
* Name：Span的名称，主要用于界面展示，一般是接口方法名。
* TimeStamp：Span创建的时间戳。
* Duration：从Span的创建到Span完成所经历的时间。

所以Duration在跟踪树中将表示成该Span的时间条的长度，而TimeStamp+Duration可表示出Span调用的结束时间。

#### Annotation数据
* cs - Client Start，客户端发起请求
* sr - Server Receive，服务端接收请求
* ss - Server Start，服务端完成处理，并将结果发送给客户端
* cr - Client Receive，客户端接收服务端返回信息

> sr-cs = 请求发出的延迟
ss-sr = 服务端处理延迟
cr-cs = 得到整个链路的完成延迟

#### BinaryAnnotation数据
服务标注列表，提供一些额外的信息如url地址、返回码和异常信息等

