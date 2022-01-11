---
title: spring-boot笔记--安全
date: 2020-02-19 11:23:01
tags:
- Spring Boot
- Spring Security
categories:
- Spring Boot
- Spring Security
permalink: spring-boot-security
---

# 十一、Spring Boot与安全

应用程序的两个主要区域是“认证”和“授权”（或者访问控制），这两个区域是Spring Security的两个目标

+ “认证”（Authentication），是建立一个它声明的主体的过程（一个“个体”一般是指用户，设备或一些可以在你的应用程序中执行动作的其它系统）
+ “授权”（Authorization），指确定一个主体是否允许在你的应用程序执行一个动作的过程。为了抵达需要授权的店，主体的身份已经有认证过程建立
+ 这个概念是通用的而不是只在Spring Security中

<!-- more -->

## 1、登录、认证、授权

首先引入Spring Security

```xml        &lt;dependency&gt;
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

定制自己的配置类

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // super.configure(http);
        // 定制请求的授权规则
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/hello/**").hasRole("user")
                .antMatchers("/data/**").hasRole("admin");

        // 开启自动配置的登录功能
        // /login来到登录页
        // 错误来到/login?error
        // loginPage("/userlogin")自己的登录页
        // 默认post请求的/login代表处理登录，一旦自己定制登录页，loginPage的post请求就是登录
        // usernameParameter,passwordParameter设置登录表单的name
        http.formLogin()
            .usernameParameter("user").passwordParameter("pwd")
            .loginPage("/userlogin");

        // 开启自动配置的注销功能
        // 访问logout表示用户注销，清空session
        // 注销成功返回到登录页面
        // logoutSuccessUrl("/")注销成功来到首页
        http.logout().logoutSuccessUrl("/");

        // 开启记住我功能
        // 登录成功后，将cookie发给浏览器保存，以后访问页面会带上这个cookie
        // 点击注销会删除cookie
        // rememberMeParameter设置checkbox的name
        http.rememberMe().rememberMeParameter("remember");
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // super.configure(auth);
        // 定制认证规则
        auth.inMemoryAuthentication()
                .withUser("zhangsan").password("123456").roles("user")
                .and()
                .withUser("lisi").password("qwer").roles("data");
    }
}
```

