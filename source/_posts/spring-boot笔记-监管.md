---
title: spring-boot笔记--监管
date: 2020-02-19 16:48:39
tags:
- Spring Boot
- Actuator
categories:
- Spring Boot
- Actuator
permalink: spring-boot-actuator
---

# 十三、Spring Boot与监管

## 1、监控端点测试

Spring Boot可以为我们提供准生产环境下的应用监控和管理功能。我们可以通过HTTP，JMX，SSH协议来进行操作，自动得到审计、健康及指标信息

<!-- more -->

| id             | 描述                                                         | 默认是否启用 |
| -------------- | ------------------------------------------------------------ | ------------ |
| auditevents    | 显示当前应用程序的审计事件信息                               | Yes          |
| beans          | 显示应用Spring Beans的完整列表                               | Yes          |
| caches         | 显示可用缓存信息                                             | Yes          |
| conditions     | 显示自动装配类的状态及及应用信息                             | Yes          |
| configprops    | 显示所有 @ConfigurationProperties 列表                       | Yes          |
| env            | 显示 ConfigurableEnvironment 中的属性                        | Yes          |
| flyway         | 显示 Flyway 数据库迁移信息                                   | Yes          |
| health         | 显示应用的健康信息（未认证只显示status，认证显示全部信息详情） | Yes          |
| info           | 显示任意的应用信息                                           | Yes          |
| liquibase      | 展示Liquibase 数据库迁移                                     | Yes          |
| metrics        | 展示当前应用的 metrics 信息                                  | Yes          |
| mappings       | 显示所有 @RequestMapping 路径集列表                          | Yes          |
| scheduledtasks | 显示应用程序中的计划任务                                     | Yes          |
| sessions       | 允许从Spring会话支持的会话存储中检索和删除用户会话。         | Yes          |
| shutdown       | 允许应用以优雅的方式关闭（默认情况下不启用）                 | No           |
| threaddump     | 执行一个线程dump                                             | Yes          |
| httptrace      | 显示HTTP跟踪信息（默认显示最后100个HTTP请求 - 响应交换）     | Yes          |
| heapdump       | 返回一个GZip压缩的hprof堆dump文件                            | Yes          |
| prometheus     | Prometheus服务器抓取的格式显示metrics信息                    | Yes          |

只需要配置文件

```properties
# 放开Actuator Web REST 端点,否则访问不到
management.endpoint.beans.enabled=true
management.endpoints.web.base-path=/actuator
management.endpoints.web.exposure.include=*
```

访问localhost:8080/actuator/health，可以知道应用健康信息

### 2、自定义端点

我们可以按照两种策略来自定义：

- @Endpoint 同时支持JMX和http
- @JmxEndpoint 只支持JMX技术
- @WebEndpoint 只支持http

通过在一个端点类上添加上面其中一个来表明该类是一个端点类。
 在类的方法使用@ReadOperation，@WriteOperation或@DeleteOperation，这分别会映射到Http中的 GET、POST、DELETE（对http来说）。  以下是我们自定义的一个端点：

```java
@Component
@Endpoint(id = "ceshi")
public class FeaturesEndpoint {

   private Map<String, Feature> features = new ConcurrentHashMap<>();

   @ReadOperation
   public Map<String, Feature> features() {
       return features;
   }

   @ReadOperation
   public Feature feature(@Selector String name) {
       return features.get(name);
   }

   @WriteOperation
   public void configureFeature(@Selector String name, Feature feature) {
       features.put(name, feature);
   }

   @DeleteOperation
   public void deleteFeature(@Selector String name) {
       features.remove(name);
   }

   public static class Feature {
       private Boolean enabled;

       // getters、setters方法 
   }

}
```