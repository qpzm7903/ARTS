spring家族，spring framewrok，spring boot，spring cloud

## spring boot

spring boot做了什么？

> It covers topics such as build systems, auto-configuration, and how to run your applications

https://spring.io/projects/spring-boot

特性：

- 创建独立的 Spring 应用程序
- 内置Tomcat、Jetty或Undertow（无需部署WAR文件）
- 提供 `starter`简化依赖
- 尽可能自动配置 Spring 和 3rd 方库
- 完全不需要代码生成，也不需要 XML 配置

spring boot maven plugin

https://docs.spring.io/spring-boot/docs/2.5.6/maven-plugin/reference/htmlsingle/#?.?

提供很多插件，比如打包，运行，热加载（spring-boot-devtools）。

> 热加载在intellij上需要做一些设置。
> 
> 如果直接通过mvn spring-boot:run ，就不需要那么多配置

自定义启动横幅

banner.txt

应用事件和侦听器

- ApplicationStartingEvent 
- ApplicationEnvironmentPreparedEvent
- ApplicationContextInitializedEvent
- ApplicationPreparedEvent
- ApplicationStartedEvent
- AvailabilityChangeEvent
- ApplicationReadyEvent
- AvailabilityChangeEvent
- ApplicationFailedEvent

Web环境

MVC 、Fluent，default

accessing application argument

applicationRunner or CommandLineRunner

在springApplication起来的时候就执行，可以实现这两个接口。

外部配置

加密配置

EnvironmentPostProcessor 这个接口允许维护环境变量

profile

logging

使用 commons loggin 作为门面，实现可以选择 java unit logging，log4j2，logback，slf4j等

颜色

文件输出

文件轮转

日志级别

日志组

用于方便设置日志级别

自定义日志配置

不同的日志实现有不同的配置文件

JSON

提供Gson、Jackson，JSON-B

### 开发web应用

内置Tomcat、Jetty，Undertow，Netty。

- spring web mvc 框架
  - 自动配置
    - 支持index.html
    - http消息转换器
    - 自定义JSON序列化和反序列化器
    - 消息码解析器
    - 静态资源
      - 可以配置路径
      - 也有默认路径
    - welcome page
      - 也就是index.html

Spring WebFlux Framework

- new reactive web framework introduced in Spring Framework 5.0
- is fully asynchronous and non-blocking, and implements the Reactive Streams specification through the Reactor project

JAX-RS and Jersey

JAX-RS programming model for REST endpoints，spring支持使用jersy，apache cxf。

Embedded Servlet Container Support

Embedded Reactive Server Support

### RSocket

> RSocket is a binary protocol for use on byte stream transports. It enables symmetric interaction models via async message passing over a single connection.

### Security

1. mvc security
2. webflux security
3. oauth2.0
4. saml 2.0
5. actuator security

### working with sql database

- configure datasource
  
  - 嵌入式
  - 其他
  - 连接池
  - 

- jdbcTemplate

- jpa and spring jap

- using JOOQ
  
  - >  jOOQ Object Oriented Querying (jOOQ) is a popular product from Data Geekery which generates Java code from your database and lets you build type-safe SQL queries through its fluent API

- wirking with nosql technologies
  
  - mongodb
    - support embedded mongodb
  - neo4j
  - elasticsearch
  - redis
  - gemFire or Geode
  - Cassandra
  - Couchbase
  - LDAP

### caching

> At its core, the abstraction applies caching to methods, thus reducing the number of executions based on the information available in the cache

- supported cache provider
  - generic
  - jCache
  - EnCache 2.x
  - Hazelcast
  - infinispan
  - couchbase
  - redis
  - caffeine
  - simple

### messaging

JMS、AMQP、Kafka

support embedded kafka

### validation

> `javax.validation` constraints on parameteres or value

> `@Validated` annotation at the type level

### distributed transactions with jta

### Quartz Scheduler

### spring Session

https://spring.io/projects/spring-session

> Spring Session provides an API and implementations for managing a user’s session information.

### Testing

- depencency
- test srping boot application
  - detecting web type
  - detecing test configuration
  - excluding test configuration
  - Using Application Arguments
  - esting with a mock environment
  - Testing with a running server
  - Mocking and Spying Beans
    - @MockBean
    - @SpyBean
  - Auto-configured Tests
  - 

## spring cloud

https://spring.io/projects/spring-cloud#overview

> Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems
> 
> 开发分布式应用

分布式应用一般需要

> configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state

特性提供

- Distributed/versioned configuration
- Service registration and discovery
- Routing
- Service-to-service calls
- Load balancing
- Circuit Breakers 断路器？
- Global locks
- Leadership election and cluster state
- Distributed messaging


