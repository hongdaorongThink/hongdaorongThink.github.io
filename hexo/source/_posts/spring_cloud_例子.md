# spring_cloud_例子

[1]: https://cloud.spring.io/spring-cloud-static/Greenwich.SR2/single/spring-cloud.html
[1]: https://spring.io/projects/spring-cloud

Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state).

Spring Cloud 为开发者提供了工具
用于快速构建通用模式的分布式系统
例如: 配置管理, 服务发现, 断路器, 智能路由, 微代理, 控制总线, 一次性token, 全局锁, 主节点选择, 分布式session, 集群状态

Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand up services and applications that implement those patterns. 

使用样板代码就能快速实现分布式应用 

They will work well in any distributed environment, including the developer’s own laptop, bare metal data centres, and managed platforms such as Cloud Foundry.

在各种环境下都能良好运行


```java 
@SpringBootApplication
@EnableDiscoveryClient
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

只需通过注解就能启动需要的功能

## 主要项目

Main Projects

### Spring Cloud Config

Centralized external configuration management backed by a git repository. The configuration resources map directly to Spring Environment but could be used by non-Spring applications if desired.

分布式配置中心, 

## Spring Cloud Netflix

Integration with various Netflix OSS components (Eureka, Hystrix, Zuul, Archaius, etc.).

服务注册和发现, 客户端负载均衡,  断路器, 智能路由

## Spring Cloud Bus

An event bus for linking services and service instances together with distributed messaging. Useful for propagating state changes across a cluster (e.g. config change events).

消息总线, 

## Spring Cloud Cloudfoundry

Integrates your application with Pivotal Cloud Foundry. Provides a service discovery implementation and also makes it easy to implement SSO and OAuth2 protected resources.

集成 Pivotal Cloud Foundry 云服务

## Spring Cloud Open Service Broker

Provides a starting point for building a service broker that implements the Open Service Broker API.

用于构建实现Open Service Broker API的Spring Boot应用程序的框架


## Spring Cloud Cluster

Leadership election and common stateful patterns with an abstraction and implementation for Zookeeper, Redis, Hazelcast, Consul.

集群功能, 抽象了 Zookeeper , redis, hazelcast, consul 

## Spring Cloud Consul

Service discovery and configuration management with Hashicorp Consul.

服务治理

## Spring Cloud Security

Provides support for load-balanced OAuth2 rest client and authentication header relays in a Zuul proxy.

负载均衡, 服务授权

## Spring Cloud Sleuth

Distributed tracing for Spring Cloud applications, compatible with Zipkin, HTrace and log-based (e.g. ELK) tracing.

服务跟踪

## Spring Cloud Data Flow

A cloud-native orchestration service for composable microservice applications on modern runtimes. Easy-to-use DSL, drag-and-drop GUI, and REST-APIs together simplifies the overall orchestration of microservice based data pipelines.

大数据处理

## Spring Cloud Stream

A lightweight event-driven microservices framework to quickly build applications that can connect to external systems. Simple declarative model to send and receive messages using Apache Kafka or RabbitMQ between Spring Boot apps.

消息驱动

## Spring Cloud Stream App Starters

Spring Cloud Stream App Starters are Spring Boot based Spring Integration applications that provide integration with external systems.


## Spring Cloud Task

A short-lived microservices framework to quickly build applications that perform finite amounts of data processing. Simple declarative for adding both functional and non-functional features to Spring Boot apps.

定时任务

## Spring Cloud Task App Starters

Spring Cloud Task App Starters are Spring Boot applications that may be any process including Spring Batch jobs that do not run forever, and they end/stop after a finite period of data processing.


## Spring Cloud Zookeeper

Service discovery and configuration management with Apache Zookeeper.

自动配置 zookeeper 

## Spring Cloud AWS

Easy integration with hosted Amazon Web Services. It offers a convenient way to interact with AWS provided services using well-known Spring idioms and APIs, such as the messaging or caching API. Developers can build their application around the hosted services without having to care about infrastructure or maintenance.

自动配置 aws 服务

## Spring Cloud Connectors

Makes it easy for PaaS applications in a variety of platforms to connect to backend services like databases and message brokers (the project formerly known as "Spring Cloud").

云服务连接自动配置

## Spring Cloud Starters

Spring Boot-style starter projects to ease dependency management for consumers of Spring Cloud. (Discontinued as a project and merged with the other projects after Angel.SR2.)


## Spring Cloud CLI

Spring Boot CLI plugin for creating Spring Cloud component applications quickly in Groovy

方便创建应用

## Spring Cloud Contract

Spring Cloud Contract is an umbrella project holding solutions that help users in successfully implementing the Consumer Driven Contracts approach.

消费者驱动契约

## Spring Cloud Gateway

Spring Cloud Gateway is an intelligent and programmable router based on Project Reactor.

网关, 替代 zuul 

## Spring Cloud OpenFeign

Spring Cloud OpenFeign provides integrations for Spring Boot apps through autoconfiguration and binding to the Spring Environment and other Spring programming model idioms.

OpenFeign 自动配置

## Spring Cloud Pipelines

Spring Cloud Pipelines provides an opinionated deployment pipeline with steps to ensure that your application can be deployed in zero downtime fashion and easilly rolled back of something goes wrong.


## Spring Cloud Function

Spring Cloud Function promotes the implementation of business logic via functions. It supports a uniform programming model across serverless providers, as well as the ability to run standalone (locally or in a PaaS).
