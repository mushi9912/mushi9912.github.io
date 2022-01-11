---
title: spring-boot笔记--分布式
date: 2020-02-19 12:31:49
tags:
- Spring Boot
- ZooKeeper
- Dubbo
- Eureka
categories:
- Spring Boot
- Dubbo
- Eureka
- ZooKeeper
permalink: spring-boot-distributed
---

# 十二、Spring Boot与分布式

## 1、分布式应用

在分布式系统中，国内常用zookeeper+dubbo组合，而Spring Boot推荐使用全栈的Spring，Spring Boot+Spring Cloud

## 2、ZooKeeper、Dubbo

**ZooKeeper**是一个分布式的，开放源码的分布式应用程序协调服务。它是一个分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等

**Dubbo**是Alibaba开源的分布式服务框架，它的最大特点是按照分层的方式来架构，使用这种方式可以是各个层之间解耦合（或者最大限度的松耦合）。从服务模型的角度来看，Dubbo采用的是一种非常简单的模型，要么是提供方提供服务，要么是消费方消费服务，所以基于这一点可以抽象出提供方（Provider）和消费服务方（Consumer）两个角色

<!-- more -->

## 3、Docker安装ZooKeeper

```shell
安装
docker pull zookeeper
运行这里只指定了2181端口
docker run --name mykeeper -p 2181:2181 --restart always -d [id]
```

## 4、使用Dubbo

首先创建一个空项目，新建模块provider-ticket（服务提供者）和consumer-user（用户）

这里注意依赖很重要，本人是Spring Boot2.2.4，所用依赖为

```xml
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.7.3</version>
</dependency>

<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.5.7</version>
</dependency>

<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>4.2.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.2.0</version>
</dependency>
```

1. 将服务提供者注册到注册中心

   ```java
   在service包下创建一个接口
   public interface TicketService {
       public String getTicket();
   }
   
   编写一个实现类
   @Component
   // 注意是Dubbo的Service
   @Service
   public class TicketServiceImpl implements TicketService{
       @Override
       public String getTicket() {
           return "三体";
       }
   }
   
   @EnableDubbo
   @SpringBootApplication
   public class ProviderTicketApplication {
   ```
   
   配置文件
   
   ```properties
   dubbo.application.name=provider-ticket
   dubbo.registry.address=zookeeper://192.168.1.18:2181
   dubbo.scan.base-packages=com.ms.ticket.service
   ```
   
2. 在消费者测试

   ```java
   // 先把服务提供者的service包里的接口复制过来，注意包的路径要完全一样
   // 然后在UserService下
   @Component
   @Service
   public class UserService {
   
       @Reference
       TicketService ticketService;
   
       public void hello(){
           String ticket = ticketService.getTicket();
           System.out.println("买到票了"+ticket);
       }
   }
   
   // 进行测试
   @Autowired
   UserService userService;
   
   @Test
   void contextLoads() {
       userService.hello();
   }
   ```

   配置文件

   ```properties
   dubbo.application.name=consumer-user
   dubbo.registry.address=zookeeper://192.168.1.18:2181
   ```

## 5、Spring Cloud-Eureka

创建空项目，在创建consumer-user，eureka-server，provider-ticket三个项目

**eureka-server**配置和使用

```yaml
server:
  port: 8761
eureka:
  instance:
    hostname: eureka-server  #eureka实例的主机名
  client:
    register-with-eureka: false  # 不把本身也注册在eureka里
    fetch-registry: false  #不从eureka上来获取服务注册信息
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {
```

**provider-ticket**配置和使用

```yaml
server:
  port: 8001
spring:
  application:
    name: provider-ticket
eureka:
  instance:
    prefer-ip-address: true  #注册服务使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

```java
@Service
public class TicketService {

    public String getTicket(){
        return "地心引力";
    }
}


@RestController
public class TicketController {
    @Autowired
    TicketService service;
    @GetMapping("/ticket")
    public String getTicket(){
        return service.getTicket();
    }
}
```

**consumer-user**配置和使用

```yaml
server:
  port: 8200
spring:
  application:
    name: consumer-user
eureka:
  instance:
    prefer-ip-address: true  #注册服务使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

```java
@RestController
public class UserController {

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/buy")
    public String buyTicket(String name){
        // 请求/ticket
        String a = restTemplate.getForObject("http://PROVIDER-TICKET/ticket", String.class);
        return name+"购买了"+a;
    }
}


// 开启发现服务功能
@EnableDiscoveryClient
@SpringBootApplication
public class ConsumerUserApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerUserApplication.class, args);
    }

    // 使用负载均衡机制
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}

```

