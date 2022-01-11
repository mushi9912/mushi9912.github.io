---
title: spring-boot笔记--日志
date: 2019-12-30 20:42:32
tags:
- Spring Boot
- 日志
categories:
- Spring Boot
permalink: spring-boot-log
---

# 三、Spring Boot日志

## 1、日志框架

日志的抽象层：JCL、SLF4J、jboss-logging

日志的实现：Log4j JUL、Log4j2 Logback

Spring Boot：底层是Spring、Spring默认是JCL、而Spring Boot选用SLF4j和logback

<!-- more -->

## 2、SLF4j

### 2.1 系统中如何使用

开发的时候，日志记录方法的调用应该调用日志抽象层的方法

首先导入slf4j的jar和logback的jar，可以查看下图

{% asset_img  concrete-bindings.png 抽象层到框架的方法--来自slf4j官网 %}

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

每一个日志都有自己的配置文件，使用slf4j后还是使用日志框架自己本身的配置文件

### 2.2 多个日志框架统一转成slf4j

如何让系统中所有的日志统一到slf4j

{% asset_img  legacy.png 多个框架统一到slf4j的方法--来自slf4j官网 %}

1. 首先将系统的其它日志框架排除
2. 用中间包来替换原来的日志框架
3. 导入slf4j来实现统一框架

### 2.3 Spring Boot日志关系

我们可以创建一个新的项目，在pom.xml文件右键点击Diagrams-->Show Dependencies，可以看到idea为我们画好的依赖图

其中**spring-boot-starter**是我们最基本的文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.2.2.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

Spring Boot使用它来做我们的日志功能

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
    <version>2.2.2.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

我们可以在依赖图下查看我们的依赖关系，可以看到**spring-boot-starter-logging**依赖logback-classic来使用logback进行日志记录，其它还有xxx-to-slf4j来将其它的日志框架转换为slf4j

Spring Boot底层也是使用slf4j+logback的方式来进行日志记录，Spring Boot也把其它的日志也转换成slf4j，如果我们要用其它日志框架，一定要把日志的框架依赖移除掉，Spring Boot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志

### 2.4 Spring Boot日志的使用

1. 默认配置

   Spring Boot帮我们配置好了日志
   
   ```java
   Logger logger = LoggerFactory.getLogger(getClass());
   
   @Test
   void contextLoads() {
   
       //日志的级别trace<debug<info<warn<error
       //我们可以自己调整输出的级别
   	//Spring Boot默认使用的是info级别的，只输出info、warn、error
       logger.trace(() -> "trace");
       logger.debug(() -> "debug");
       logger.info(() -> "info");
       logger.warn(() -> "warn");
       logger.error(() -> "error");
   }
   ```
   
2. 改变配置

   在application.properties里写入

   ```properties
   logging.level.com.包名=trace
   ```

   可以更改日志级别，没有指定就用Spring Boot默认指定的级别，root级别

   ```properties
   # 会在根目录下生成spboot.log
   logging.file.name=spboot.log
   # 制定完整的路径，可在对应路径下找到
   logging.file.name=G:/spboot.log
   # 在指定路径下创建spring.log，/splog/log是在所在磁盘根目录下创建，name和path同时指定name起作用
   logging.file.path=G:/splog/log
   # 指定控制台输出日志的格式
   logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
   # 指定文件中输出日志的格式
   logging.pattern.file=%d{yyyy-MM-dd} = [%thread] = %-5level = %logger{50} = - %msg%n
   
   日志的输出格式：
   	%d：表示日期和时间
   	%thread：表示线程的名字
   	%-5level：表示级别从左显示5个字符宽度
   	%logger{50}：表示logger名字最长为50个字符，否则按照句点分割
   	%msg：日志消息
   	%n：换行
   ```

3. 指定配置

   如果我们要用自己的配置，那么在类路径下放上每个日志框架自己的配置文件，然后Spring Boot就不会使用自己的配置了

   [官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-logging)

   | Logging System          | Customization                                                |
   | ----------------------- | ------------------------------------------------------------ |
   | Logback                 | **logback-spring.xml**, **logback-spring.groovy**, **logback.xml**, or **logback.groovy** |
   | Log4j2                  | **log4j2-spring.xml** or **log4j2.xml**                      |
   | JDK (Java Util Logging) | **logging.properties**                                       |

   logback.xml直接被日志框架识别

   logback-spring.xml由Spring Boot加载配置项

   ```xml
   <springProfile name="staging">
       <!-- configuration to be enabled when the "staging" profile is active -->
       <!-- 可以指定在某个环境下生效 -->
   </springProfile>
   
   <springProfile name="dev | staging">
       <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
   </springProfile>
   
   <springProfile name="!production">
       <!-- configuration to be enabled when the "production" profile is not active -->
   </springProfile>
   ```

### 2.5 Spring Boot日志框架的切换

可以按照官网的适配图进行切换，可以查看**2.2**下的图片，我们可以通过生成依赖图的方式对依赖进行管理，具体引入哪些依赖可以查看[官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#using-boot-starter)，删除哪些依赖可以查看2.2下的图片

