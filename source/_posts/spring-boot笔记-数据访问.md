---
title: spring-boot笔记--数据访问
date: 2020-02-01 17:26:56
tags:
- Spring Boot
- JDBC
- Druid
- MySQL
- MyBatis
- JPA
- Spring Data
categories:
- Spring Boot
- Spring Data
- MyBatis
- JDBC
permalink: spring-boot-data
---

# 五、Spring Boot与数据访问

## 1、简介

Spring Boot在数据访问层，无论是SQL还是NOSQL，Spring Boot默认使用Spring Data进行整合，添加了大量的自动配置，引入了各种xxxTemplate，xxxRepository来简化我们对数据访问层的操作，我们只需要进行简单的设置即可

## 2、整合基本的JDBC和数据源

我们使用基本的JDBC和MySQL驱动，先在pom.xml引入依赖

<!-- more -->

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

然后在application.properties中写入

```properties
spring:
  datasource:
    username: root
    password: xxxxxx
    url: jdbc:mysql://localhost:3306/jdbc?serverTimezone=UTC
    # 如果不加?serverTimezone=UTC会报错
    driver-class-name: com.mysql.cj.jdbc.Driver
    # driver-class-name可以不用加
```

我们在ApplicationTests中测试

```java
@Autowired
DataSource dataSource;

@Test
void contextLoads() throws SQLException {
    System.out.println(dataSource.getClass());
    Connection connection = dataSource.getConnection();
    System.out.println(connection);
    connection.close();
}
```

我们可以查看数据源相关配置的源文件,都在**DataSourceProperties**里面

```java
@ConfigurationProperties(
    prefix = "spring.datasource"
)
public class DataSourceProperties implements BeanClassLoaderAware, InitializingBean {
```

自动配置原理

自动配置都在org.springframework.boot.autoconfigure.jdbc里面

1. 参考DataSourceConfiguration，根据相关配置创建数据源，默认使用Tomcat连接池，可以指定spring.datasource.type的属性来配置自定义的数据源

2. Spring Boot默认可以支持BasicDataSource、HikariDataSource、org.apache.tomcat.jdbc.pool.DataSource、

3. 自定义数据源

   ```java
   @Configuration(
       proxyBeanMethods = false
   )
   @ConditionalOnMissingBean({DataSource.class})
   @ConditionalOnProperty(
       name = {"spring.datasource.type"}
   )
   static class Generic {
       Generic() {
       }
   
       @Bean
       DataSource dataSource(DataSourceProperties properties) {
           //initializeDataSourceBuilder返回DataSourceBuilder创建数据源，利用反射响应type的数据源，并且绑定相关属性
           return properties.initializeDataSourceBuilder().build();
       }
   }
   ```

4. 现在看DataSourceAutoConfiguration类，它引入了一个DataSourceInitializationConfiguration类，而DataSourceInitializationConfiguration类又引入了一个DataSourceInitializerInvoker类

   DataSourceInitializerInvoker类作用，可以自动运行建表语句和插入数据的sql语句

   ```properties
   schema-*.sql  data-*.sql
   默认写为：schema.sql或者schema-all.sql都可以
   也可以自己设置路径
   
   spring:
     datasource:
       username: root
       password: 123456
       url: jdbc:mysql://localhost:3306/jdbc?serverTimezone=UTC
       //Spring Boot2.0以上都得配置initialization-mode: always
       initialization-mode: always
       //设置自定义sql文件路径
       schema:
         - classpath:sql_schema.sql
   
   这里还有我自己遇到的一个坑
   sql文件里的创建表语句必须在末尾加上';'，例如下面如果最后一个')'后不加';'是可以在Navicat里运行的，但是在Spring Boot的sql文件里必须加上';'，否则会报错
   CREATE TABLE IF NOT EXISTS `jdbc_student_schema`(
   		`id` INTEGER AUTO_INCREMENT,
   		`name` VARCHAR(10) NOT NULL,
   		`gender` VARCHAR(1) not null,
   		`card` VARCHAR(16) not null,
   		`date` date,
   		PRIMARY KEY(`id`)
   );
   ```

5. 自动配置了JdbcTemplate来操作数据库

   ```java
   @Controller
   public class HelloController {
   
       @Autowired
       JdbcTemplate jdbcTemplate;
   
       @ResponseBody
       @GetMapping("/hello")
       public Map<String, Object> hello(){
   
           List<Map<String, Object>> maps = jdbcTemplate.queryForList("select * from jdbc_student");
   
           System.out.println(maps);
   
           return maps.get(0);
   
       }
   }
   ```

   访问localhost:8080/hello，数据返回了

## 3、整合Druid数据源

**配置属性**

Druid Spring Boot Starter 配置属性的名称完全遵照 Druid，你可以通过 Spring Boot 配置文件来配置Druid数据库连接池和监控，如果没有配置则使用默认值。

- JDBC 配置

```properties
spring.datasource.druid.url= # 或spring.datasource.url= 
spring.datasource.druid.username= # 或spring.datasource.username=
spring.datasource.druid.password= # 或spring.datasource.password=
spring.datasource.druid.driver-class-name= #或 spring.datasource.driver-class-name=
```

- 连接池配置

```properties
spring.datasource.druid.initial-size=
spring.datasource.druid.max-active=
spring.datasource.druid.min-idle=
spring.datasource.druid.max-wait=
spring.datasource.druid.pool-prepared-statements=
spring.datasource.druid.max-pool-prepared-statement-per-connection-size= 
spring.datasource.druid.max-open-prepared-statements= #和上面的等价
spring.datasource.druid.validation-query=
spring.datasource.druid.validation-query-timeout=
spring.datasource.druid.test-on-borrow=
spring.datasource.druid.test-on-return=
spring.datasource.druid.test-while-idle=
spring.datasource.druid.time-between-eviction-runs-millis=
spring.datasource.druid.min-evictable-idle-time-millis=
spring.datasource.druid.max-evictable-idle-time-millis=
spring.datasource.druid.filters= #配置多个英文逗号分隔
....//more
```

- 监控配置

```properties
# WebStatFilter配置，说明请参考Druid Wiki，配置_配置WebStatFilter
spring.datasource.druid.web-stat-filter.enabled= #是否启用StatFilter默认值false
spring.datasource.druid.web-stat-filter.url-pattern=
spring.datasource.druid.web-stat-filter.exclusions=
spring.datasource.druid.web-stat-filter.session-stat-enable=
spring.datasource.druid.web-stat-filter.session-stat-max-count=
spring.datasource.druid.web-stat-filter.principal-session-name=
spring.datasource.druid.web-stat-filter.principal-cookie-name=
spring.datasource.druid.web-stat-filter.profile-enable=

# StatViewServlet配置，说明请参考Druid Wiki，配置_StatViewServlet配置
spring.datasource.druid.stat-view-servlet.enabled= #是否启用StatViewServlet（监控页面）默认值为false（考虑到安全问题默认并未启动，如需启用建议设置密码或白名单以保障安全）
spring.datasource.druid.stat-view-servlet.url-pattern=
spring.datasource.druid.stat-view-servlet.reset-enable=
spring.datasource.druid.stat-view-servlet.login-username=
spring.datasource.druid.stat-view-servlet.login-password=
spring.datasource.druid.stat-view-servlet.allow=
spring.datasource.druid.stat-view-servlet.deny=

# Spring监控配置，说明请参考Druid Github Wiki，配置_Druid和Spring关联监控配置
spring.datasource.druid.aop-patterns= # Spring监控AOP切入点，如x.y.z.service.*,配置多个英文逗号分隔
```

## 4、整合MyBatis

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
```

配置application.yml

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/jdbc?serverTimezone=UTC
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      initial-size: 5
      min-idle: 5
      max-active: 20
      max-wait: 60000
      time-between-eviction-runs-millis: 60000
      min-evictable-idle-time-millis: 300000
      validation-query: SELECT 1 FROM DUAL
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      pool-prepared-statements: true
      filters: stat, wall, slf4j
      max-pool-prepared-statement-per-connection-size: 20
      use-global-data-source-stat: true
      connect-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
      stat-view-servlet:
        enabled: true
        url-pattern: /druid/*
        login-username: admin
        login-password: 123456
```

最后创建与数据表对应的JavaBean

### 4.1、注解版MyBatis

创建一个mapper类

```java
//指定这是一个操作数据库的mapper
@Mapper
public interface StudentMapper {

    @Select("select * from jdbc_student where id=#{id}")
    public Student getStudentById(Integer id);

    @Delete("delete from jdbc_student where id=#{id}")
    public int deleteById(Integer id);
	
    @Options(useGeneratedKeys = true, keyProperty = "id")
    @Insert("insert into jdbc_student(name, gender, card, date) values(#{name}, #{gender}, #{card}, #{date})")
    public int insertStudent(Student student);

    @Update("update jdbc_student set name=#{name}, gender=#{gender}, card=#{card}, date=#{date} where id=#{id}")
    public int updateStudent(Student student);

}
```

在controller里测试它

```java
@RestController
public class StudentController {

    @Autowired
    StudentMapper studentMapper;

    @GetMapping("/stu/{id}")
    public Student getStudent(@PathVariable("id") Integer id){

        return studentMapper.getStudentById(id);

    }

    @GetMapping("/stu")
    public Student insertStudent(Student student){
        studentMapper.insertStudent(student);
        return student;
    }


}
```

自定义MyBatis的配置规则，只需要在容器中添加一个ConfigurationCustomizer

```java
@Configuration
public class MyBatisConfig {

    @Bean
    public ConfigurationCustomizer configurationCustomizer(){
        return new ConfigurationCustomizer() {
            @Override
            public void customize(org.apache.ibatis.session.Configuration configuration) {
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }

}
```

批量扫描mapper，这样就不必在每个mapper前面加@Mapper注解

```java
@MapperScan(value = "com.xxx.xxx.mapper")
```

### 4.2、配置版MyBatis

创建另一个mapper类和对应 的Javabean

```java
public interface ShareMapper {

    public Share getShareById(Integer id);

    public void insertShare(Share share);

    public void deleteShare(Integer id);

    public void updateShare(Share share);

}
```

创建全局配置文件mybatis-config.xml和mapper对应的xml文件ShareMapper.xml

```xml
<!--ShareMapper.xml-->
    
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.spring.mybatis.mapper.ShareMapper">
    <select id="getShareById" resultType="com.spring.mybatis.bean.Share" >
        select * from jdbc_share where id=#{id}
    </select>
    
    <insert id="insertShare" parameterType="com.spring.mybatis.bean.Share" useGeneratedKeys="true" keyProperty="id">
        insert into jdbc_share(tscode, name) value (#{tscode}, #{name})
    </insert>

    <delete id="deleteShare" parameterType="com.spring.mybatis.bean.Share">
        delete jdbc_share where id=#{id}
    </delete>

    <update id="updateShare" parameterType="com.spring.mybatis.bean.Share">
        update jdbc_student set tscode=#{tscode}, name=#{name} where id=#{id}
    </update>

</mapper>
```

```xml
<!--mybatis-config.xml-->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--驼峰命名法-->
    </settings>
</configuration>
```

然后在spplication.yml中配置

```yaml
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  # 全局配置文件
  mapper-locations: classpath:mybatis/mapper/*.xml
  # 所有在路径下的.xml文件
```

## 5、整合JPA

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

JPA也是基于ORM思想的，即Object、Relational、Mapping

1. 编写一个实体类（bean）和数据表进行映射，并且配置好映射关系

   ```java
   @Entity()
   @Table(name = "jdbc_share")
   public class Share {
   
       //这是一个主键
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Integer id;
   
       //这是和数据表对应的一个列
       @Column
       private String tscode;
       @Column
       private String name;
       
       //Getter、Setter方法
   ```

2. 编写一个Dao接口来操作实体类对应的数据表

   ```java
   //继承JpaRepository完成对数据库的操作
   public interface ShareRepository extends JpaRepository<Share, Integer> {
   
   }
   
   ```

3. yml基本配置

   ```yaml
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/jdbc
       username: root
       password: 123456
       driver-class-name: com.mysql.cj.jdbc.Driver
   
     jpa:
       hibernate:
         # 更新或创建表结构
         ddl-auto: update
       # 控制台显示sql
       show-sql: true
   ```

在controller里进行查找插入

```java
@RestController
public class ShareController {

    @Autowired
    ShareRepository shareRepository;

    @GetMapping("/share/{id}")
    public Share getShare(@PathVariable("id") Integer id){
        Share share = shareRepository.findById(id).get();
        return share;
    }

    @GetMapping("/share")
    public Share insertShare(Share share){
        Share save = shareRepository.save(share);
        return save;
    }
}

```



