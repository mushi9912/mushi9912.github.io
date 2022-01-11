---
title: spring-boot笔记--缓存
date: 2020-02-02 16:21:33
tags:
- Spring Boot
- cache
- 缓存
- Redis
categories:
- Spring Boot
- cache
- Redis
permalink: spring-Boot-cache
---

# 六、Spring Boot与缓存

## 1、Spring缓存抽象

Spring定义了org.springframework.cache.Cache和org.springframework.cache.CacheManager接口来统一不同的缓存技术，并支持使用JCache（JSR-107）注解简化我们的开发

<!-- more -->

## 2、几个重要概念和缓存注解

| 组件和注解     | 功能                                                         |
| -------------- | ------------------------------------------------------------ |
| Cache          | 缓存接口，定义缓存操作，实现有: RedisCache、EhCacheCache、ConcurrentMapCache 等 |
| CacheManager   | 缓存管理器，管理各种缓存（Cache）组件                        |
| @Cacheable     | 主要对方法进行配置，能够根据方法的请求参数对其结果继续缓存   |
| @CacheEvict    | 清空缓存                                                     |
| @CachePut      | 保证方法被调用、又希望结果被缓存                             |
| @EnableCaching | 开启基于注解的缓存                                           |
| keyGenerator   | 缓存数据时key生成策略                                        |
| serialize      | 缓存数据时value序列化策略                                    |

## 3、Spring Boot缓存的使用

1. 搭建基本环境、数据库表、javabean封装数据

2. 整合mybatis操作数据库，关于整合mybatis可以看之前的是[Spring Boot与数据访问](http://www.codestn.com/spring-boot-data/#more)

3. 开启基于注解的缓存

   ```java
   @EnableCaching
   //开启缓存
   @MapperScan(value = "com.cache.springboot.mapper")
   @SpringBootApplication
   public class SpringbootApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(SpringbootApplication.class, args);
       }
   
   }
   ```

   ```java
       //将方法的结果进行缓存，以后再要相同的数据，直接从缓存中获取，不会调用方法
       @Cacheable(cacheNames = "share")
       public Share getShareById(Integer id){
           Share shareById = shareMapper.getShareById(id);
           return shareById;
       }
   ```

   我们可以查看@Cacheable里的属性

   + cacheNames/value：指定缓存组件的名字

     关于缓存组件的名字：CacheManager管理多个Cache组件，对缓存真正的CRUD操作在Cache组件中，每一个缓存组件有自己唯一一个名字

     我们可以传入数组，将数据放入多个缓存里边

   + key：缓存数据使用的key，可以用它来指定，默认是使用方法参数的值，我们可以编写SpELl指定
   
     举例：指定key为getShare[1]、getShare[2]......
   
     表达式写为key="#root.methodName+'['+#id+']'"
   
     | 名字          | 位置               | 描述                                                         | 示例                                  |
     | ------------- | ------------------ | ------------------------------------------------------------ | ------------------------------------- |
     | methodName    | root object        | 当前被调用的方法名                                           | #root.methodName                      |
     | method        | root object        | 当前被调用的方法                                             | #root.method.name                     |
   | target        | root object        | 当前被调用的目标对象                                         | #root.target                          |
     | targetClass   | root object        | 当前被调用的目标对象类                                       | #root.targetClass                     |
   | args          | root object        | 当前被调用方法的参数列表                                     | #root.args[0]                         |
     | caches        | root object        | 当前方法调用使用的缓存列表（如@Cacheable(value{"cache1", "cache2"})），则有两个cache | #root.caches[0].name                  |
   | argument name | evaluation context | 方法参数的名字，可以直接#参数名，也可以使用#p0或#a0的形式，0代表参数的索引 | #ban、#a[0]、#p0如果是类可以#Share.id |
     | result        | evaluation context | 方法执行后的返回值（仅当方法执行后的判断有效，如"unless"，"cache put"的表达式，"cache evict"的表达式,beforeInvocation=false），注意@Cacheable注解的方法key不能使用#result得到返回值，因为方法需要在运行前就生成一个key | #result                               |

   + keyGenerator：key的生成器，可以自己指定key的生成器的组件id，默认的key就是用一个keyGenerator生成的，key和keyGenerator二选一使用

     ```java
     @Configuration
     public class MyCacheConfig {
     
         @Bean("myKeyGenerator")
         public KeyGenerator keyGenerator(){
             return new KeyGenerator(){
     
                 @Override
                 public Object generate(Object o, Method method, Object... objects) {
                     return method.getName()+"["+ Arrays.asList(objects).toString()+"]";
                 }
             };
         }
     }
     ```
   
     
   
   + cacheManager：指定缓存管理器，如redis，或者cacheResolver指定缓存解析器，二选一
   
   + condition：指定符合条件的情况下才缓存
   
     举例：condition="#a0>1 and ......" 第一个参数的值大于一才缓存
   
   + unless：否定缓存，当unless指定的条件为true，方法的返回值就不会被缓存，可以获取到结果进行判断
   
   + sync：是否使用异步模式
   
4. 缓存的原理

   自动配置类：CacheAutoConfiguration

   ```java
   //在CacheAutoConfiguration里有这个类的selectImports方法可以得到所有的配置类，Spring Boot自动配置缓存的时候引入了很多的配置类
   static class CacheConfigurationImportSelector implements ImportSelector {
       CacheConfigurationImportSelector() {
       }
   
       public String[] selectImports(AnnotationMetadata importingClassMetadata) {
           CacheType[] types = CacheType.values();
           String[] imports = new String[types.length];
   
           for(int i = 0; i < types.length; ++i) {
               imports[i] = CacheConfigurations.getConfigurationClass(types[i]);
           }
   
           return imports;
       }
   }
   ```

   默认生效的配置有SimpleCacheConfiguration
   
   SimpleCacheConfiguration给缓存注册了一个cacheManager
   
   ConcurrentMapCacheManager的作用
   
   ```java
   @Bean
   //SimpleCacheConfiguration给缓存注册了一个cacheManager：ConcurrentMapCacheManager，可以获取和创建ConcurrentMapCacheManager类型的缓存组件，将数据存取在ConcurrentMap中
   ConcurrentMapCacheManager cacheManager(CacheProperties cacheProperties, CacheManagerCustomizers cacheManagerCustomizers) {
       ConcurrentMapCacheManager cacheManager = new ConcurrentMapCacheManager();
       List<String> cacheNames = cacheProperties.getCacheNames();
       if (!cacheNames.isEmpty()) {
           cacheManager.setCacheNames(cacheNames);
       }
   
       return (ConcurrentMapCacheManager)cacheManagerCustomizers.customize(cacheManager);
   }
   ```
   
   运行的流程@Cacheable
   
   1. 方法运行前，先去检查缓存Cache（缓存组件），按照cacheNames指定的名字获取（CacheManager先获取相应的缓存），第一次获取缓存如果没有会自动创建
   
   2. 去Cache中查找缓存的内容，使用一个key，默认是方法的参数，key是按照某种策略生成的，策略使用keyGenerator生成，默认使用SimpleKeyGenerator生成key
   
      SimpleKeyGenerator生成key的策略：
   
      如果没有参数key=new SimpleKey()
   
      有一个参数key=参数的值
   
      有多个参数key=new SimpleKey(params)
   
   3. 没有查到缓存就调用目标方法
   
   4. 将目标方法返回的结果放进缓存中
   
   @CacheEvict
   
   + key：指定要清除的缓存
   
   + allEntries=true：指定清除这个缓存中的所有数据
   
   + beforeInvocation=false：缓存的清除是否在方法之前执行
   
     默认是在方法之后执行，例如方法中出现异常就不会清除
   
   @Caching
   
   ```java
   //定义复杂的缓存规则
   @Caching(
           cacheable = {
               @Cacheable(cacheNames = "share", key="#result.id")
           },
           put = {
               @CachePut(cacheNames = "share", key="#result.tscode")
           }
   )
   ```
   
   @CacheConfig
   
   ```java
   //定义全局的cacheconfig
   @CacheConfig(cacheNames = "share")
   ```

## 4、搭建Redis环境和整合Redis

百度Redis的安装，十分简单

整合Redis

```java
@Autowired
RedisTemplate redisTemplate;  //操作k-v字符串的

@Autowired
StringRedisTemplate stringRedisTemplate;  //操作k-v对象的

@Autowired
RedisTemplate<Object, Object> myredisTemplate;

@Test
public void testRedis(){
    stringRedisTemplate.opsForValue();  //操作String字符串
    stringRedisTemplate.opsForList();  //操作list列表
    stringRedisTemplate.opsForSet();  //操作set集合
    stringRedisTemplate.opsForHash();  //操作Hash散列
    stringRedisTemplate.opsForZSet();  //操作ZSet有序集合

    //默认保存对象，会使用JDK的序列化机制，序列化后的数据保存在Redis中
    //如果我们想用json格式保存数据，可以自己配置相应的Config，如下面的MyRedisConfig类
    redisTemplate.opsForValue();  //与上述类似，不过可以存入对象
    myredisTemplate.opsForValue().set("ceshi", shareMapper.getShareById(1));
    System.out.println(myredisTemplate.opsForValue().get("ceshi"));
}
```

自己的RedisConfig

```java
@Configuration
public class MyRedisConfig {

    @Bean
    public RedisTemplate<Object, Object> myredisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(new Jackson2JsonRedisSerializer<Object>(Object.class));
        return template;
    }

}
```

注意如果在Redis命令行查询，先通过`keys *`命令查看所有key，可以看到Redis中的key为`"\"ceshi\""`，所以直接`get ceshi`是无法查到的