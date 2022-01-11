---
title: spring-boot笔记--消息
date: 2020-02-13 14:13:27
tags:
- Spring Boot
- RabbitMQ
- 消息
categories:
- Spring Boot
- RabbitMQ
permalink: spring-boot-rabbitmq
---

# 七、Spring Boot与消息

## 1、概述

1. 大多数应用中、可通过消息服务中间件来提升系统异步通信、扩展解耦能力

2. 消息服务中两个重要概念：**消息代理**、**目的地**

   当消息发送者发送消息后，将由消息代理接管，消息代理保证消息传递到指定目的地

   <!-- more -->

3. 消息队列主要有两种形式的目的地

   + 队列（queue）：点对点消息通信
   + 主题（topic）：发布（publish）/订阅（subscribe）消息通信

4. 点对点式：

   + 消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取信息内容，信息读取后被移出队列
   + 消息只有唯一一个发送者和接受者，但并不是说只能有一个接收者

5. 发布订阅式：

   + 发送者（发布者）发送消息到主题，多个接收者（订阅者）监听（订阅）这个主题，那么就会在消息到达时同时收到消息

6. JMS（Java Message Service）JAVA消息服务

   + 基于JVM消息代理的规范。ActiveMQ、HornetMQ是JMS实现

7. AMQP（Advanced Message Queuing Protocol）

   + 高级消息队列协议，也是一个消息代理的规范，兼容JMS
   + RabbitMQ是AMQP的实现

8. Spring支持

   + spring-jms提供了对JMS的支持
   + spring-rabbit提供了对AMQP的支持
   + 需要ConnectionFactory的实现来连接消息代理
   + 提供了JMSTemplate、RabbitTemplate来发送消息
   + @JMSListener（JMS）、@RabbitListener（AMQP）注解在方法上监听消息代理发布的消息
   + @EnableJms、@EnableRabbit开启支持

9. Spring Boot自动配置

   + JMSAutoConfiguration
   + RabbitAutoConfiguration

JMS和AMQP的对比

|              | JMS                                                          | AMQP                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义         | Java api                                                     | 网络线级协议                                                 |
| 跨语言       | 否                                                           | 是                                                           |
| 跨平台       | 否                                                           | 是                                                           |
| Model        | 提供两种消息模型：Peer-2-Peer、Pub/Sub                       | 提供了五种消息模型：direct exchange、fanout exchange、topic exchange、headers exchange、system exchange。本质来讲、后四种和JMS的pub/sub模型没有太大差别，仅是在路由机制上做了更详细的划分 |
| 支持消息类型 | 多种消息类型：TextMessage、MapMessage、BytesMessage、StreamMessage、ObjectMessage、Message（只有消息头和属性） | bytes[]，当实际使用时，有更复杂的消息，可以将消息序列化后发送 |
| 综合评价     | JMS定义了JAVA API层面的标准，在Java体系中，多个client均可以通过JMS进行交互，不需要应用修改代码，但是对跨平台的支持较差 | AMQP定义了wire-level层的协议标准天然具有跨平台、跨语言特性   |

## 2、运行RabbitMQ

```shell
docker run -d -p 5672:5672 -p 15672:15672 --name myrabbit rabbitmq
```

## 3、Spring Boot的RabbitMQ操作

**RabbitMQ的自动配置**

自动配置类**RabbitAutoConfiguration.class**

+ 自动配置了连接工厂  CachingConnectionFactory.class
+ 配置属性  RabbitProperties.class
+ RabbitTemplate.class 给RabbitMQ发送和接收消息
+ AmqpAdmin.class RabbitMQ的启动管理组件，创建删除Queue、Exchange和Binding
+ @EnableRabbit + @RabbitListener监听消息队列

据RabbitProperties，我们可以设置配置属性

```properties
spring.rabbitmq.host=192.168.1.18
#spring.rabbitmq.username=guest
#spring.rabbitmq.password=guest
##spring.rabbitmq.virtual-host= 默认访问'/'
```

接下来我们就可以发送消息进行测试

```java
@Autowired
RabbitTemplate rabbitTemplate;

@Test
void contextLoads() {

    // 单播（点对点）
    // message可以自己构造，搜索Message类，定义消息体内容和消息头
    // rabbitTemplate.send(exchange, routeKey, message);
    // 常用的发送，传入一个object可以自己序列化发送给MQ
    // rabbitTemplate.convertAndSend(exchange, routeKey, object);
    Map<String, Object> map = new HashMap<>();
    map.put("msg", "测试消息");
    map.put("list", Arrays.asList("list消息", 123, true));
    //对象被默认序列化以后发送出去
    // RabbitTemplate里的属性使用的是SimpleMessageConverter
    // private MessageConverter messageConverter = new SimpleMessageConverter();
    rabbitTemplate.convertAndSend("ms.direct", "news", map);
    //如何将数据变为json发送
}

@Test
void receive(){
    //接收后消息队列为空
    Object receive = rabbitTemplate.receiveAndConvert("ms.news");
    System.out.println(receive.getClass());
    System.out.println(receive);
}
```

rabbitTemplate设置

```java
// 配置会自动生效，因为在RabbitAutoConfiguration类里有一行代码
// messageConverter.ifUnique(template::setMessageConverter);
// 其中ifUnique会将我们自己设置的配置进来
@Bean
public MessageConverter messageConverter(){
    return new Jackson2JsonMessageConverter();
}
```

## 4、监听消息队列

**@EnableRabbit** + **@RabbitListener**监听消息队列

```java
@RabbitListener(queues = "ms.news")
public void getShare(Share share){
    System.out.println("收到消息");
}
@RabbitListener(queues = "ms.news")
public void getHeader(Message message){
    System.out.println(message.getBody());
    System.out.println(message.getMessageProperties());
}
```

```java
//开启基于注解的RabbitMQ
@EnableRabbit
@SpringBootApplication
public class SpringbootAmqpApplication {
```

## 5、创建Exchange、Queue和Binding

```java
@Test
void createExchange(){
    // 创建Exchange和Queue
    // amqpAdmin.declareExchange(new DirectExchange("amqpadmin.exchange"));
    // amqpAdmin.declareQueue(new Queue("amqpadmin.queue", true));
    // 创建绑定规则
    // amqpAdmin.declareBinding(new Binding("amqpadmin.queue", Binding.DestinationType.QUEUE, "amqpadmin.exchange", "amqpadmin", null));
    // System.out.println("创建完成");
}
```

