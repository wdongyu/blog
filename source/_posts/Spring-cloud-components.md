---
title: Spring_cloud_components
date: 2017-09-21 00:42:40
tags:
---

### Spring_cloud 子项目总结：
1. Spring Cloud Config（分布式配置）@EnableConfigureServer：将各个应用的配置信息进行集中同一的管理，利于提高生产效率，同时保证了系统开发环境和测试运行环境的一致性。支持用户将信息传到git仓库进行存储，也可以进行本地存储；
2. Spring Cloud Netflix： 
    * Eureka（服务发现）：创建服务注册中心@EnableEurekaClient，接收其余需要注册的服务并提供给用户；创建提供服务的客户端@EnableDiscoryClient，向服务注册中心进行服务的注册和发现。
**两两注册的方式可以实现集群中节点完全对等的效果，实现最高可用性集群，任何一台注册中心故障都不会影响服务的注册与发现**
    * Hystrix（断路器）：在微服务架构中，由于服务之间的相互依赖关系，一旦某个服务单元出现故障，将会导致故障的恶性蔓延，进而导致系统的崩溃。因此使用断路器来通过控制这些节点，及时地向服务调用方返回一个错误响应而不是使其长时间的等待，来保证系统的稳定。
    * Ribbon/Feign（负载平衡）：对服务注册中心中提供相同服务的客户端列表进行轮询访问，实现负载平衡；
    * Zuul（智能路由）：Zuul一是实现了前述的负载平衡及断路器的功能，同时实现了路由功能来屏蔽了诸多的服务细节；更为重要的是实现了服务过滤的功能，将访问控制权限与提供微服务业务两者分离开。
3. Spring Cloud Bus（消息总线）：当系统中某项配置发生变动时，向Config Server中心发起`/bus/refresh`的POST请求，然后Config Server再向消息总线发送变更消息，最后由总线将更新后的内容发送至与Destination参数相匹配的目标服务实例。  
**通过上面的改动，我们的服务实例就不需要再承担触发配置更新的职责。同时，对于Git的触发等配置都只需要针对Config Server即可，从而简化了集群上的一些维护工作。**

![](http://blog.didispace.com/assets/5-7.png)


> http://blog.didispace.com/Spring-Cloud基础教程/