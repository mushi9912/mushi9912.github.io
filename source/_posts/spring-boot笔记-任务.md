---
title: spring-boot笔记--任务
date: 2020-02-19 09:43:37
tags:
- Spring Boot
- Async
categories:
- Spring Boot
- Async
permalink: spring-boot-task
---

# 十、Spring Boot与任务

## 1、异步任务

- **同步方法**调用一旦开始，调用者必须等到方法调用返回后，才能继续后续的行为。
- **异步方法**调用一旦开始，方法调用就会立即返回，调用者就可以继续后续的操作。而，异步方法通常会在另外一个线程中，“真实”地执行着。整个过程，不会阻碍调用者的工作

<!-- more -->

```java
// 告诉Spring Boot这是一个异步的方法
@Async
public void hello(){
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("正在处理......");
}

@Autowired
AsyncService asyncService;

@GetMapping("/hello")
public String hello(){
    asyncService.hello();
    return "success";
}

// 开启异步注解
@EnableAsync
@SpringBootApplication
public class SpringbootTaskApplication {
```

## 2、定时任务

```java
// second(秒)、minute(分)、hour(时)、day of month(日)、month(月)、day of week(周几)
// 0 * * * * MON-FRI  周一到周五每一分钟0时都启动
// @Scheduled(cron = "0 * * * * WED")
// @Scheduled(cron = "0,1,2,3,4 * * * * WED")
// @Scheduled(cron = "0-4 * * * * WED")
// @Scheduled(cron = "0/4 * * * * WED")  每四秒执行一次
public void hello(){
    System.out.println("Hello");
}

// 开启定时注解
@EnableScheduling
@SpringBootApplication
public class SpringbootTaskApplication {
```

**cron表达式**

| 字段 | 允许值                | 允许的特殊字符  |
| ---- | --------------------- | --------------- |
| 秒   | 0-59                  | , - * /         |
| 分   | 0-59                  | , - * /         |
| 小时 | 0-23                  | , - * /         |
| 日期 | 1-31                  | , - * ? / L W C |
| 月份 | 1-12                  | , - * /         |
| 星期 | 0-7或SUN-SAT 0,7是SUN | , - * ? / L C # |

**特殊字符的含义**

| 特殊字符 | 代表含义                   |
| -------- | -------------------------- |
| ,        | 枚举                       |
| -        | 区间                       |
| *        | 任意                       |
| /        | 步长                       |
| ?        | 日/星期冲突匹配            |
| L        | 最后                       |
| W        | 工作日                     |
| C        | 和calendar联系后计算过的值 |
| #        | 星期，4#2，第2个星期四     |

## 3、邮件任务

首先设置一个邮箱，开启stmp和pop3服务，生成授权码，为了安全起见，我们不会使用密码，而是使用授权码发送邮件

导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

Spring Boot配置

```properties
spring.mail.username=xxxxxxxxxxx@163.com
spring.mail.password=xxxxxxxx
spring.mail.host=smtp.163.com
```

发送简单邮件

```java
@Test
void contextLoads() {
    SimpleMailMessage mailMessage = new SimpleMailMessage();
    mailMessage.setSubject("springboot邮件任务");
    mailMessage.setText("20200219邮件测试");
    mailMessage.setTo("xxxxxxxxxx@qq.com");
    mailMessage.setFrom("xxxxxxxxxxx@163.com");
    javaMailSender.send(mailMessage);
}
```

发送复杂邮件

```java
@Test
void test2(){
    // 创建一个复杂的消息邮件
    MimeMessage mimeMessage = javaMailSender.createMimeMessage();
    MimeMessageHelper mimeMessageHelper = null;
    try {
        mimeMessageHelper = new MimeMessageHelper(mimeMessage, true);
        mimeMessageHelper.setSubject("springboot邮件任务");
        mimeMessageHelper.setText("<b style='color: red'>20200219复杂邮件测试</b>", true);
        mimeMessageHelper.setTo("xxxxxxxxxx@qq.com");
        mimeMessageHelper.setFrom("xxxxxxxxxxx@163.com");
        mimeMessageHelper.addAttachment("测试文件.jpg", new File("测试文件.jpg"));
        javaMailSender.send(mimeMessage);
    } catch (MessagingException e) {
        e.printStackTrace();
    }
}
```

