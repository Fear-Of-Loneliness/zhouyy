---
layout: page
title: "SpringBoot"
description: "基于springboot的分布式框架搭建"
subtitle:  	基于springboot的分布式框架搭建
header-img: "https://zhouyy.top/img/home-bg.jpg"
---

### 什么是分布式？

就多个人在一起做不同的事，共同把一件大的事情拆分成每一个小的事情。

所以我们把一个大的服务拆分成每一个小的服务，让他们分工不同，解决了传统项目的模块耦合度，并且提高了项目的扩展性。

##### 图解：

[![springboot](https://zhouyy.top/img/springboot/分布式.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/分布式.png)

技术的话采用的是springboot + springcloud，将每个模块做成微服务springboot，然后服务注册在注册中心eureka中，基于feignclient进行远程调用。

[![springboot](https://zhouyy.top/img/springboot/a.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/a.png)

### 搭建springboot服务

我们首先得知道怎么搭建一个springboot应用，搭建springboot很简单，只需要4部就能搭起一个springboot。

1. 创建一个maven项目

[![springboot](https://zhouyy.top/img/springboot/创建maven项目.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/创建maven项目.png)

[![springboot](https://zhouyy.top/img/springboot/maven项目.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/maven项目.png)

2. 添加pom坐标

[![springboot](https://zhouyy.top/img/springboot/添加pom坐标.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/添加pom坐标.png)

##### Parent的作用

- 默认配置文件

- 集成插件

- 版本管理

##### Web作用

 - 集成spring和springmvc

3. 添加配置文件

[![springboot](https://zhouyy.top/img/springboot/添加properties配置文件.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/添加properties配置文件.png)

4. 添加启动类

[![springboot](https://zhouyy.top/img/springboot/添加启动类.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/添加启动类.png)

那么Springboot就已经搭建成功，如果验证他成功了呢？跑起来查看控制台，在控制台能看到项目启动端口和启动耗时。

[![springboot](https://zhouyy.top/img/springboot/是否启动成功.png){:height="250" width="800"}](https://zhouyy.top/img/springboot/是否启动成功.png)

### 搭建注册中心eureka

Springboot成功之后，这个架构已经完成了三分之一，还剩下两部分，下一步需要搭建一个注册中心来实现服务与服务之间的注册和发现

1. 同样先创建一个springboot项目

2. 添加pom坐标

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

3. 配置文件中添加配置

![springboot](https://zhouyy.top/img/springboot/注册中心properties配置.png){:height="250" width="800"}

```properties
spring.application.name=center
server.port=1120
eureka.instance.hostname=localhost
#表示是否将自己注册到Eureka Server，默认为true
eureka.client.register-with-eureka=false
#表示是否从Eureka Server获取注册信息，默认为true。
eureka.client.fetch-registry=false
#设置与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址。默认是http://localhost:8761/eureka ；多个地址可使用 , 分隔。
eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```

4. 启动类添加注解

```java
@EnableEurekaServer
```

5. 将服务注册到注册中心

- 添加pom坐标

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 在配置文件中添加配置

```properties
eureka.client.serviceUrl.defaultZone=http://localhost:1120/eureka/
eureka.client.healthcheck.enabled=true
eureka.instance.lease-expiration-duration-in-seconds=30
eureka.instance.lease-renewal-interval-in-seconds=10
```

- 在启动类中添加注解

```java
@EnableFeignClients
```

### 服务消费