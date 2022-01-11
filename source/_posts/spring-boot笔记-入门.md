---
title: spring boot笔记--入门
date: 2019-12-25 19:52:57
tags:
- Spring Boot
- 入门
categories:
- Spring Boot
permalink: spring-boot-start
---

# 一、Spring Boot入门

## 1、简介

[Spring官网](https://spring.io/)

Spring boot来简化Spring应用开发的一个框架，整个Spring技术栈的一个大整合，约定大于配置，去繁从简，just run就能创立一个独立的，产品级别的应用

**背景：** J2EE笨重的开发、配置繁多，复杂的部署流程、第三方技术集成压力大

<!-- more -->

**解决：**

+ Spring Boot --> J2EE一站式解决方案
+ Spring Cloud --> 分布式整体解决方案

**优点：**

+ 快速创建独立运行的Spring项目以及主流框架集成
+ 使用嵌入式的Servlet容器，应用无需打成WAR包
+ starters自动依赖和版本控制
+ 大量的自动配置，简化开发，也可修改默认值
+ 无需配置XML，无代码生成，开箱即用
+ 准生产环境的运行时应用监控
+ 与云计算的天然集成

**缺点：** 易学难精



## 2、微服务

2014年，Martin Fowler写了一篇文章，微服务是一种架构风格，一个应用应该是一组小型服务，可以通过HTTP进行通信。每一个功能元素都是可独立替换和升级的单元。

Spring Boot可以快速构建一个应用，Spring Cloud进行网状互连互调，Spring Cloud Data Flow在分布式中进行流式计算和批处理

{% asset_img spring流程.jpg Spring项目流程 %}

***

{% blockquote James Lewis and Martin Fowler (2014) https://martinfowler.com/microservices What are Microservices? %}
In short, the microservice architectural style is an approach to developing a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API. These services are built around business capabilities and independently deployable by fully automated deployment machinery. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies.
{% endblockquote %}

***

{% blockquote James Lewis and Martin Fowler (2014) https://translate.google.cn/ 谷歌翻译 %}
简而言之，微服务架构风格是一种将单个应用程序开发为一组小服务的方法，每个小服务都在自己的进程中运行并与轻量级机制（通常是HTTP资源API）进行通信。 这些服务围绕业务功能构建，并且可以由全自动部署机制独立部署。 这些服务的集中管理几乎没有，可以用不同的编程语言编写并使用不同的数据存储技术。
{% endblockquote %}



## 3、Hello World

### 3.1 快速创建一个Spring Boot项目

+ File-->New-->Project，选中Spring Initializr，默认点击next

  {% asset_img springboot1.jpg %}

+ 填写Group、Artifact、Package，点击next

  {% asset_img springboot2.jpg %}

+ 选择导入的模块，这里我们先只选Spring Web，点击next

  {% asset_img springboot3.jpg %}

+ 填写Project name，点击Finish

  {% asset_img springboot4.jpg %}

+ 在com.springboot.helloworld右键New Class，写入controller.HelloController，点击OK，在HelloController下写入

  ```java
  @RestController
  public class HelloController {
      @RequestMapping("/hello")
      public String hello(){
          return "Hello Spring boot ";
      }
  }
  ```

+ 启动主程序，在浏览器写入localhost:8080/hello即可成功

+ 注意resources文件夹下
  + static：保存所有的静态资源，如：js、css、images
  + templates：保存所有的模板界面
  + application.properties：Spring Boot的应用配置文件马可以修改一些默认设置



### 3.2 POM文件

#### 3.2.1 父项目

pom.xml文件开头引入父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

按住**Ctrl**点击**spring-boot-starter-parent**，可以看到这个父项目还依赖一个父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

再点击**spring-boot-dependencies**，可以看到定义了许多依赖的版本，他来管理**Spring Boot**里的所有依赖版本，以后我们导入依赖默认是不需要导入版本的(没有在**dependencies**里面的依赖自然需要声明版本号)

```xml
<properties>
    <activemq.version>5.15.11</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.77</appengine-sdk.version>
    <artemis.version>2.10.1</artemis.version>
    <aspectj.version>1.9.5</aspectj.version>
    <assertj.version>3.13.2</assertj.version>
    <atomikos.version>4.0.6</atomikos.version>
    <awaitility.version>4.0.1</awaitility.version>
    <bitronix.version>2.1.4</bitronix.version>
    <build-helper-maven-plugin.version>3.0.0</build-helper-maven-plugin.version>
    <byte-buddy.version>1.10.4</byte-buddy.version>
    ......
```

#### 3.2.2 导入的依赖

**JAR**包是有谁导入的，我们继续看**pom.xml**可以看到

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

可以看到**spring-boot-starter-web**与父项目的**spring-boot-starter-parent**有一个共有的**spring-boot-starter**

**spring-boot-starter**是Spring Boot**场景启动器**

点击**spring-boot-starter-web**可以看到它也有一些依赖导入

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.2.2.RELEASE</version>
        <scope>compile</scope><!-- 依赖spring-boot-starter基础项目 -->
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-json</artifactId>
        <version>2.2.2.RELEASE</version><!-- 依赖json -->
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <version>2.2.2.RELEASE</version><!-- 依赖tomcat -->
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
        <version>2.2.2.RELEASE</version><!-- 依赖数据校验 -->
        <scope>compile</scope>
        <exclusions>
            <exclusion>
                <artifactId>tomcat-embed-el</artifactId>
                <groupId>org.apache.tomcat.embed</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

帮我们导入了web模块正常运行所依赖的组件

Spring Boot将所有的场景功能都抽取出来，做成一个个starters(启动器)，只需要在项目里引入这些starter，相关场景的所有依赖都会导入进来。

### 3.3 主程序类

#### 3.3.1 注解

```java
@SpringBootApplication()
public class HelloWorldMainApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }
}
```

+ **@SpringBootApplication()**

Spring Boot标注在某个类上说明这个类是Spring Boot的主配置类，Spring Boot就应该运行这个类的main方法来启动Spring Boot应用

我们点击它来查看，发现它是一个组合注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

+ **@SpringBootConfiguration**

Spring Boot配置，它标注在某个类上表示这个一个Spring Boot配置类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
```

点击**@SpringBootConfiguration**，可以看到**@Configuration**，这就是Spring最底层的一个注解，配置类上来标注这个注解。配置类------配置文件，配置类也是一个组件**@Component**。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
```

+ **@EnableAutoConfiguration**

还可以看到一个**@EnableAutoConfiguration**，作用是开启自动配置功能，Spring Boot帮我们自动配置，我们可以点开查看

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

**@AutoConfigurationPackage**自动配置包

点开来看

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
```

**@Import({Registrar.class})**，**@import**：Spring的底层注解，给容器中导入一个组件，点击**Registrar.class**，查看这个类

可以看到下面这个方法

```java
public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
    AutoConfigurationPackages.register(registry, (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName());
}
```

我们在**AutoConfigurationPackages.register**这一行加一个断点，并debug运行，可以发现**metadata**是我们注解的原信息**@SpringBootApplication()**，我们还可以拉动鼠标选中

```java
(new AutoConfigurationPackages.PackageImport(metadata)).getPackageName()
```

点击右键选择**Evaluate Expression**，点击**Evaluate**按钮，可以看到result就等于我们的包名

所以**@AutoConfigurationPackage**就是将主配置类**@SpringBootApplication()**标注的类所在包及下面所有子包里面的所有组件扫描到容器中

**@EnableAutoConfiguration**里还有一个注解

**@Import({AutoConfigurationImportSelector.class})**，点进去查看

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
```

发现**selectImports**将所有需要导入的组件全类名的方式返回，这些组件就会被添加到容器中，我们通过debug可以发现导入的组件中有很多的自动配置类，就是给容器导入场景所需要的组件并配置好

