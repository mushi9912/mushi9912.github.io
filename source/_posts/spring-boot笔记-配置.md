---
title: spring-boot笔记--配置
date: 2019-12-27 22:12:05
tags:
- spring boot
- 配置
categories:
- Spring Boot
permalink: spring-boot-config
---

# 二、Spring Boot配置

## 1、配置文件

Spring Boot使用一个全局的配置文件，配置文件是固定的

+ application.properties
+ application.yml

配置文件的作用：修改Spring Boot自动配置的默认值

<!-- more -->

在底层Spring Boot都给我们配置好了，你可以自己修改配置

## 2、YAML

YAML (YAML Ain't a Markup Language)，它既是一个标记语言又不是一个标记语言，以数据为中心，比json、xml等更适合做配置文件

如果要配置端口，在**application.properties**中

```properties
server.port=80
```

在**application.yml**中

```yaml
server: 
  port: 80
```

### 2.1 YAML语法

k: v: 表示一对键值对(空格必须有)，以空格缩进表示层级关系，大小写是敏感的，缩进不允许使用tab，只允许空格，缩进的空格数不重要，只要相同层级的元素左对齐即可

```yaml
server:
  port: 80
  path: /hello
```

值得写法

+ 普通的值(数字、字符串、布尔)

  直接来写，字符串默认不加上单引号或双引号

  ""：双引号，不会转义字符串，特殊字符会作为本身想表示的意思

  ''：单引号，会转义特殊字符，特殊字符会变为普通字符串

+ 对象、Map(键值对)

  ```yaml
  student: 
    name: liming
    age: 18
  ```

  行内写法

  ```yaml
  student: {name: liming, age: 18}
  ```

+ 数组(List、set)

  ```yaml
  student: 
    -liming
    - zhangsan
  ```

  行内写法

  ```yaml
  student: {liming, zhangsan}
  ```

## 3、配置文件值获取

在包里新建bean.student类，并写入

```java
public class Student {
    private String name;
    private String sex;
    private Integer age;
    private boolean cadre;
    private List<String> course;
    private Map<String, Object> score;
    private Card card;
	...... //下面是Getter,Setter,toString方法
```

在resources文件夹下新建文件application.yml，并写入

```yaml
student:
  name: zhangsan
  sex: male
  age: 21
  cadre: true
  course: {math, english}
  score: {math: 100, english: 100}
  card:
    number: 123456
    money: 10000
```

将配置的每一个数据映射到组件中，在组件上方加入

```java
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
```

发现idea会有提示，在pom.xml中写入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

此为配置文件处理器，配置文件和组件连接就会有提示**@ConfigurationProperties(prefix = "student")**是告诉本类中的所有属性和配置文件中的相关配置进行连接，**prefix = "student"**是与配置文件student下面的所有属性进行一一映射

**@Component**只有这个组件是容器中的组件，才能使用容器提供的功能

现在就可以测试了

我们进入**test**下面的**ApplicationTests**类，这是我们的Spring Boot单元测试类，可以在测试期间自动注入到容器

```java
@Autowired
Student student;

@Test
void contextLoads() {
    System.out.println(student);
}
```

在下方的Spring Boot提示中可以看到结果

Student{name='zhangsan', sex='male', age=21, cadre=true, course=null, score={math=100, english=100}, card=Card{number='123456', money=10000}}

### 3.1 properties配置

我们还可以在properties里配置，properties优先级要高

```properties
student.name=张三
student.sex=male
student.age=22
student.cadre=false
student.course=数学,英语
student.score.sx=120
student.score.yy=120
student.card.number=123456789
student.card.money=20000
```

运行test发现可以输出结果但有乱码，因为properties是ASCII码，而idea用的是utf-8，我们打开File-->Settings，搜索File Encodings，选择utf-8并打勾

{% asset_img properties配置.jpg 解决中文乱码 %}

之后运行发现解决了中文乱码问题

### 3.2 @Value和@ConfigurationProperties区别

我们注释掉**@ConfigurationProperties**

```java
@Component
//@ConfigurationProperties(prefix = "student")
public class Student {

    @Value("${student.name}")
    private String name;
    @Value("${student.sex}")
    private String sex;
    @Value("#{2*10}")
    private Integer age;
    @Value("true")
    private boolean cadre;
```

我们再次运行，发现仍然有效

|              | @ConfigurationProperties | @Value   |
| ------------ | :----------------------- | -------- |
| 功能         | 批量注入属性             | 单个指定 |
| 松散语法     | 支持-n等于大写的N        | 不支持   |
| SpEL(表达式) | 不支持                   | 支持     |
| JSR303校验   | 支持                     | 不支持   |
| 复杂类型封装 | 支持                     | 不支持   |

```java
@Component
@ConfigurationProperties(prefix = "student")
@Validated
public class Student {
    //@Value("${student.name}")
    @Email //name必须是邮箱格式
    private String name;
```

**@Validated**表示需要校验

配置文件中yml和properties都能获取到值，有一定的区别

至于**@ConfigurationProperties**和**@Value**，我们只取某个值时，可以用**@Value**，如果我们有专门的javaBean来映射配置文件，就用**@ConfigurationProperties**

### 3.3 @PropertySource、@ImportResource、@Bean

**@PropertySource**：加载指定的配置文件

**@ConfigurationProperties**：默认从全局文件中获取值

```java
@PropertySource(value = {"classpath:student.properties"})
@Component
@ConfigurationProperties(prefix = "student")
@Validated
```

**classpath**为**resources**文件夹路径

**@ImportResource**：导入Spring的配置文件，让配置文件里面的内容生效

Spring Boot不会自动识别Spring的配置文件，不能自动识别

想让Spring的配置生效，**@ImportResource**标注在配置类上

```java
@ImportResource(locations = {"classpath:bean.xml"})
@SpringBootApplication
public class HelloWorldApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class, args);
    }

}
```

Spring Boot推荐给容器中加组件的方式

+ 配置类就相当于以前的配置文件

  ```java
  @Configuration
  public class MyConfig {
      @Bean
      public HelloService helloService(){
          return new HelloService();
      }
  }
  
  ```

  @Bean将方法的返回值添加到容器中，默认ID为方法名

## 4、配置文件占位符

+ 随机数

  ```properties
  ${random.value}、${random.int}、${random.long}
  ${random.int(10)}、${random.int[1024,65536]}
  ```

+ 占位符获取之前配置的值，如果没有就作为字符串，还可指定默认:默认值

  ```properties
  student.name=张三${random.value}
  student.sex=${student.name}
  student.age=${random.int}
  student.cadre=false
  student.course=数学,英语
  student.score.sx=120
  student.score.yy=120
  student.card.number=${student.happy:happy}
  student.card.money=20000
  ```

## 5、Profile

### 5.1 多Profile文件

主配置文件编写的时候，文件名可以是application-{profile}.properties/yml

默认使用application.properties的配置

### 5.2 yml多文档块模式

```yaml
server:
  port: 8001
spring:
  profiles:
    active: dev
---
server:
  port: 8082
spring:
  profiles: dev
---
server:
  port: 8083
spring:
  profiles: pro
```



### 5.3 激活指定的profile

+ application-dev.properties，在全局配置文件中写入

  ```properties
  spring.profiles.active=dev
  ```

+ 命令行方式

  --spring.profiles.active=dev

  {% asset_img 命令行配置.jpg %}

+ 虚拟机参数

  -Dspring.profiles.active=dev

  {% asset_img 虚拟机配置.jpg %}

## 6、配置文件加载位置

Spring Boot会扫描以下位置的application.properties或者yml文件作为Spring Boot的默认配置文件

1. file:./config/

   当前项目文件夹下的config文件夹里面

2. file:./

   项目文件夹路径下

3. classpath:/config/

   类路径下的config文件夹里

4. classpath:/

   类路径的根目录

以上按优先级从高到低的顺序，所有文件都会被加载，高优先会覆盖低优先

也可通过配置spring.config.location来改变默认配置，运维时使用

## 7、外部配置加载位置

Spring Boot支持多种外部配置方式

1. **命令行参数**
2. 来自java:comp/env的JNDI属性
3. Java系统属性(System.getProperties())
4. 操作系统环境变量
5. RandomValuePropertySource配置的random.*属性值
6. **jar包外部的application-{profile}.properties或yml(带spring.profile)配置文件**
7. **jar包内部的application-{profile}.properties或yml(带spring.profile)配置文件**
8. **jar包外部的application.properties或yml(不带spring.profile)配置文件**
9. **jar包内部的application.properties或yml(不带spring.profile)配置文件**
10. @Configuration注解类上的@PropertySource
11. 通过SpringApplication.setDefaultProperties指定的默认属性

[官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config)

## 8、自动配置原理

配置文件可以配置哪些属性，可以参照[官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#common-application-properties)

**自动配置原理**

1. Spring Boot启动时加载了主配置类，开启了自动配置功能**@EnableAutoConfiguration**，然后用**AutoConfigurationImportSelector**来给容器中导入组件，详细可以查看**selectImports**方法

   ```java
   public String[] selectImports(AnnotationMetadata annotationMetadata) {
       if (!this.isEnabled(annotationMetadata)) {
           return NO_IMPORTS;
       } else {
           AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
           AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata); //上一行的getAutoConfigurationEntry对应下面方法
           return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
       }
   }
   
   protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata) {
       if (!this.isEnabled(annotationMetadata)) {
           return EMPTY_ENTRY;
       } else {
           AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
           List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
           configurations = this.removeDuplicates(configurations);
           Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
           this.checkExcludedClasses(configurations, exclusions);
           configurations.removeAll(exclusions);
           configurations = this.filter(configurations, autoConfigurationMetadata);
           this.fireAutoConfigurationImportEvents(configurations, exclusions);
           return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
       }
   }
   ```

   我们从中提取出

   ```java
   //selectImports里的代码
   AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
   
   //getAutoConfigurationEntry里的代码
   List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
   
   //getCandidateConfigurations里的代码
   List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
   
   //loadFactoryNames函数
   public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
       String factoryTypeName = factoryType.getName();
       return (List)loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
   }
   //loadSpringFactories的代码
   private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
       MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
       if (result != null) {
           return result;
       } else {
           try {
               Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
               LinkedMultiValueMap result = new LinkedMultiValueMap();
   
               while(urls.hasMoreElements()) {
                   URL url = (URL)urls.nextElement();
                   UrlResource resource = new UrlResource(url);
                   Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                   Iterator var6 = properties.entrySet().iterator();
   
                   while(var6.hasNext()) {
                       Entry<?, ?> entry = (Entry)var6.next();
                       String factoryTypeName = ((String)entry.getKey()).trim();
                       String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                       int var10 = var9.length;
   
                       for(int var11 = 0; var11 < var10; ++var11) {
                           String factoryImplementationName = var9[var11];
                           result.add(factoryTypeName, factoryImplementationName.trim());
                       }
                   }
               }
   
               cache.put(classLoader, result);
               return result;
           } catch (IOException var13) {
               throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
           }
       }
   }
   //扫描所有类路径的META-INF/spring.factories文件，把扫描到的这些文件的url得到，然后包装成Properties，然后从properties里获取一些值最后加在result里，这个result就是我们要交给容器中的所有组件，函数的参数classLoader就是从loadFactoryNames里传来的，loadFactoryNames的参数往上回溯就是上面提到的getCandidateConfigurations里的代码，再看getCandidateConfigurations的代码，查看getSpringFactoriesLoaderFactoryClass，可以看到
   
   //意思就是从properties中获取到EnableAutoConfiguration.class类名对应的值，然后把它们添加到容器中
   protected Class<?> getSpringFactoriesLoaderFactoryClass() {
       return EnableAutoConfiguration.class;
   }
   
   //然后点开一个jar包(有的有，有的没有)，打开META-INF下面的spring.factories，发现了org.springframework.boot.autoconfigure.EnableAutoConfiguration=\......(已省略)这段代码，相当于把我们省略的这部分代码加到了容器中
   ```

   **总结一下就是，将类路径下META-INF/spring.factories里面配置的EnableAutoConfiguration的值加到了容器中，每一个省略代码里的xxxxAutoConfiguration类都加入到容器中，用它们来做自动配置**

2. 每一个**xxxxAutoConfiguration**进行自动配置功能，我们以一个为例

   ```java
   @Configuration  //表示这是一个配置类，和以前的配置文件一样，也可以给容器添加组件
   @ConditionalOnClass({SqlSessionFactory.class, SqlSessionFactoryBean.class})
   //spring底层@Conditional注解，根据不同条件，如果满足条件配置类才会生效，判断jar包里有没有这些类
   @ConditionalOnSingleCandidate(DataSource.class)
   //DataSource.class已经存在应用上下文时才会加载
   @EnableConfigurationProperties({MybatisProperties.class})
   //启用MybatisProperties的ConfigurationProperties功能，并放入ioc容器
   @AutoConfigureAfter({DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class})
   //@AutoConfigureAfter是加载配置的类后再加载当前类
   public class MybatisAutoConfiguration implements InitializingBean {
       //properties这个properties已经与配置文件映射了，应为上面的@EnableConfigurationProperties
       private final MybatisProperties properties;
       ......
       //这有一个有参构造器，参数的值就会从容器中拿
       public MybatisAutoConfiguration(MybatisProperties properties, ObjectProvider<Interceptor[]> interceptorsProvider, ObjectProvider<TypeHandler[]> typeHandlersProvider, ObjectProvider<LanguageDriver[]> languageDriversProvider, ResourceLoader resourceLoader, ObjectProvider<DatabaseIdProvider> databaseIdProvider, ObjectProvider<List<ConfigurationCustomizer>> configurationCustomizersProvider) {
           this.properties = properties;
           this.interceptors = (Interceptor[])interceptorsProvider.getIfAvailable();
           this.typeHandlers = (TypeHandler[])typeHandlersProvider.getIfAvailable();
           this.languageDrivers = (LanguageDriver[])languageDriversProvider.getIfAvailable();
           this.resourceLoader = resourceLoader;
           this.databaseIdProvider = (DatabaseIdProvider)databaseIdProvider.getIfAvailable();
           this.configurationCustomizers = (List)configurationCustomizersProvider.getIfAvailable();
       }
       ......
       @Bean  //如果生效，给容器中添加一个组件，组件的某些值需要从properties中获取
       @ConditionalOnMissingBean
       public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
           SqlSessionFactoryBean factory = new SqlSessionFactoryBean();
           factory.setDataSource(dataSource);
           factory.setVfs(SpringBootVFS.class);
           if (StringUtils.hasText(this.properties.getConfigLocation())) {
               factory.setConfigLocation(this.resourceLoader.getResource(this.properties.getConfigLocation()));
           }
           }
       ......
   
   ```

   ```java
   @ConfigurationProperties(
       prefix = "mybatis"
   )  //从配置文件中获取指定的值，所有在配置类中能配置的属性都在xxxProperties在封装着
   public class MybatisProperties {
       public static final String MYBATIS_PREFIX = "mybatis";
       private static final ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
       private String configLocation;
       private String[] mapperLocations;
       private String typeAliasesPackage;
       private Class<?> typeAliasesSuperType;
       private String typeHandlersPackage;
       private boolean checkConfigLocation = false;
       private ExecutorType executorType;
       private Class<? extends LanguageDriver> defaultScriptingLanguageDriver;
       private Properties configurationProperties;
       @NestedConfigurationProperty
       private Configuration configuration;
   
       public MybatisProperties() {
       }
   ```

   总结一下就是根据当前不同的条件，决定这个配置类是否生效，如果生效@Bean给容器中添加组件，这些组件的属性是从对应的properties类中获取的，类里的每一个属性又是和配置文件联系起来的

**再次总结：**

+ Spring Boot启动会加载大量的自动配置类
+ 我们需要的功能有没有Spring Boot写好的自动配置类，再来看类里配置了哪些组件，如果有我们要用的组件，就不需要再来配置了
+ 给容器中自动配置类添加组件的时候，会从properties类中获取属性，这些属性我们就可以在配置文件中指定这些属性的值

## 9、@Conditional、自动配置报告

**@Conditional：**必须是@Conditional指定的条件成立，才给容器中添加组件，配置类里面的所有内容才生效

| @Conditional扩展注解            | 作用                                     |
| ------------------------------- | ---------------------------------------- |
| @ConditionalOnJava              | 系统的Java版本是否符合需求               |
| @ConditionalBean                | 容器中存在指定的Bean                     |
| @ConditionalOnMissingBean       | 容器中不存在指定的Bean                   |
| @ConditionalOnExpression        | 满足SpEL表达式锁定                       |
| @ConditionalOnClass             | 系统中有指定的类                         |
| @ConditionalOnMissingClass      | 系统中没有指定的类                       |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者是首选Bean |
| @ConditionalOnProperty          | 系统中的指定属性是否有指定值             |
| @ConditionalOnResource          | 类路径下是否有指定的资源文件             |
| @ConditionalOnWebApplication    | 当前是Web环境                            |
| @ConditionalOnNotWebApplication | 当前不是Web环境                          |
| @ConditionalOnJndi              | JNDI存在指定项                           |

自动配置类必须在一定条件下才能生效，我们必须知道哪些生效，我们可以在**application.properties**中写入**debug=true**，意思是开启Spring Boot的debug模式，可以看到自动配置报告，**positive matches：**自动配置类启用哪些

