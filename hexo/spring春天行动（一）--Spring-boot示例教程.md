---
title: spring春天行动（一）--Spring-boot-web项目实战教程
date: 2019-04-13 01:02:12
tags: 
    - mysql
    - mybatis
categories: 
    - 教程
    - 实战
    - spring
---

# 1.准备环境
选择Idea IDE开发，下载Idea并安装，然后修改maven源，修改办法：maven安装目录的conf目录下的settings.xml
    
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      </mirror>

# 2.新建spring-boot项目

Idea下菜单栏 File --> New Project --> Spring Initializr --> next

填写Group 和 Atifact --> next

勾选web --> web、SQL --> MySql、Mybatis --> next --> finish.

 
# 3.POM.xml
项目建好后，先看看POM.xml，检查有无异常。
主要检查三个依赖：
* spring-boot-starter-web --> spring依赖
* mybatis-spring-boot-starter --> mybatis依赖
* mysql-connector-java --> mysql连接器依赖

下面是 POM.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>springdemo5</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>springdemo5</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>2.0.1</version>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```


# 4.测试代码

用这种方法建的spring项目是可以直接启动起来的，找到src/test/java/com/example/demo/Springdemo5ApplicationTests代码，加一行测试直接运行。

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class Springdemo5ApplicationTests {

	@Test
	public void contextLoads() {
		System.out.println("hello springdemo5");
	}

}
```
报错：
```
...
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSource' defined in class path resource [org/springframework/boot/autoconfigure/jdbc/DataSourceConfiguration$Hikari.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.zaxxer.hikari.HikariDataSource]: Factory method 'dataSource' threw exception; nested exception is org.springframework.boot.autoconfigure.jdbc.DataSourceProperties$DataSourceBeanCreationException: Failed to determine a suitable driver class
...
```

找到关键信息，没有配置dataSource，所以我们需要配置数据源。

## 5.配置dataSource
，那我们就先吧dataSource配置上
找到资源文件夹 resources下面的application.properties，写以下内容（注意，我用的是mysql8.0，所以driver是com.mysql.cj.jdbc.Driver，mysql8.0以下版本driver为 com.mysql.jdbc.Driver）：

```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=test
spring.datasource.password=test

mybatis.type-aliases-package= com.example.demo
mybatis.mapper-Locations: classpath:mybatis/*.xml
```

再次test测试

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.4.RELEASE)

2019-04-13 22:13:29.129  INFO 7540 --- [           main] c.e.demo.Springdemo5ApplicationTests     : Starting Springdemo5ApplicationTests on IE361.COM-71519 with PID 7540 (started by 琪 in F:\github\springdemo5)
2019-04-13 22:13:29.129  INFO 7540 --- [           main] c.e.demo.Springdemo5ApplicationTests     : No active profile set, falling back to default profiles: default
2019-04-13 22:13:30.093  WARN 7540 --- [           main] o.m.s.mapper.ClassPathMapperScanner      : No MyBatis mapper was found in '[com.example.demo]' package. Please check your configuration.
2019-04-13 22:13:31.463  INFO 7540 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2019-04-13 22:13:32.145  INFO 7540 --- [           main] c.e.demo.Springdemo5ApplicationTests     : Started Springdemo5ApplicationTests in 3.436 seconds (JVM running for 5.044)
hello springdemo5
2019-04-13 22:13:32.575  INFO 7540 --- [       Thread-2] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'

Process finished with exit code 0
```
以上界面就是成功运行界面，当然还没有加主体内容。

# 6.spring-boot 入口

下面开始撸代码，先找到spring-boot的入口,/src/main/java/com/example/demo/Springdemo5Application，代码如下：
```
@SpringBootApplication
public class Springdemo5Application {

	public static void main(String[] args) {
		SpringApplication.run(Springdemo5Application.class, args);
	}

}
```
重要知识点：@SpringBootApplication 注解为spring-boot入口。SpringApplication.run(Springdemo5Application.class, args);为固定格式，现在先不要动。为了防止以后找不到类，和空指针异常这类的错误，在入口这里加几个注解。
```
@SpringBootApplication
@EntityScan(basePackages = "com.example.demo")
@ComponentScan(basePackages = "com.example.demo")
@MapperScan(basePackages = "com.example.demo")
public class Springdemo5Application {

	public static void main(String[] args) {
		SpringApplication.run(Springdemo5Application.class, args);
	}

}
```
注解意义为：扫描入口、组件、映射。

# 7.Controller层

控制层，新建包：com.example.demo.ControllerDemo，使用类注解 @RestController 表示这是一个控制层类，代码如下：
```
@RestController
public class ControllerDemo {
    @GetMapping("/")
    public String root(){
        return "this is a root html";
    }

    @GetMapping("/hello")
    public String hello(){
        return "say hello";
    }
}
```
Controller类两个方法，root()为登录页面主页，hello()为helloworld测试方法。

运行Springdemo5Application，启动日志：
```

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.4.RELEASE)

2019-04-13 22:28:59.086  INFO 16992 --- [           main] com.example.demo.Springdemo5Application  : Starting Springdemo5Application on IE361.COM-71519 with PID 16992 (F:\github\springdemo5\target\classes started by 琪 in F:\github\springdemo5)
2019-04-13 22:28:59.091  INFO 16992 --- [           main] com.example.demo.Springdemo5Application  : No active profile set, falling back to default profiles: default
2019-04-13 22:29:00.095  WARN 16992 --- [           main] o.m.s.mapper.ClassPathMapperScanner      : No MyBatis mapper was found in '[com.example.demo]' package. Please check your configuration.
2019-04-13 22:29:02.056  INFO 16992 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2019-04-13 22:29:02.136  INFO 16992 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2019-04-13 22:29:02.136  INFO 16992 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.17]
2019-04-13 22:29:02.416  INFO 16992 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-04-13 22:29:02.416  INFO 16992 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 3260 ms
2019-04-13 22:29:02.711  INFO 16992 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2019-04-13 22:29:03.122  INFO 16992 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-04-13 22:29:03.132  INFO 16992 --- [           main] com.example.demo.Springdemo5Application  : Started Springdemo5Application in 4.751 seconds (JVM running for 5.886)
2019-04-13 22:29:11.251  INFO 16992 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2019-04-13 22:29:11.251  INFO 16992 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2019-04-13 22:29:11.261  INFO 16992 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 10 ms
```
找到Tomcat started on port(s): 8080 (http) with context path ''这里，说明tomcat启动了。

浏览器测试：用浏览器访问 localhost:8080，返回“this is a root html”说明成功；用浏览器访问 http://localhost:8080/hello，返回“say hello”说明hello方法也成功。

# 8.service层
MVC框架的第二层，主要封装业务逻辑，被Controller层调用，我们用一些代码模拟业务处理，新建包：com.example.demo.service，包下新建service类--UserQueryService，类代码如下：
```
@Service
public class UserQueryService {
    public String queryUser() throws InterruptedException {
        System.out.println("查询预处理");
        System.out.println("业务逻辑一");
        System.out.println("业务逻辑二");
        System.out.println("业务逻辑...");

        System.out.println("业务处理中...请稍候");
        Thread.sleep(1000);

        System.out.println("业务完成后处理");
        System.out.println("释放资源");
        
        return "查询成功";
    }
}

```
service层要加@Service 注解，Controller改写，注入Service并调用，代码如下：
```
@RestController
public class ControllerDemo {

    @Autowired
    private UserQueryService userQueryService;
    
    @GetMapping("/query")
    public String query() throws InterruptedException {
        String result = userQueryService.queryUser();
        return result;
    }
}
```
测试一下，用浏览器访问 http://localhost:8080/query，显示“查询成功”，日志打印：
```
查询预处理
业务逻辑一
业务逻辑二
业务逻辑...
业务处理中...请稍候
业务完成后处理
释放资源
```
则service层建立完毕。

# 9.Dao层
在建立Dao层之前要建立pojo层，新建包： com.example.demo.dao，新建类： User，代码如下：
```
public class User {
    private int id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

然后建立持久层，使用Mybatis框架，刚才已经写好配置文件 application.properties 不再多说，新建package，com.example.demo.dao，新建接口 UserMapper，接口增加一个查询方法 queryUserById。
```
@Mapper
public interface UserMapper {
    @Select("select * from user where id = #{id}")
    User selectUserById(@Param("id") int id);
}
```
改写service调用，注入UserMapper
```
@Service
public class UserQueryService {
    @Autowired
    private UserMapper userMapper;
    
    public String queryUser(int id) {
        System.out.println("查询预处理");
        System.out.println("业务逻辑一");
        System.out.println("业务逻辑二");
        System.out.println("业务逻辑...");

        User user = userMapper.selectUserById(id);

        System.out.println("业务完成后处理");
        System.out.println("释放资源");

        return "查询成功:" + user;
    }
}
```
改写Controller
```
@RestController
public class ControllerDemo {
    @Autowired
    private UserQueryService userQueryService;

    @GetMapping("/query")
    public String query(@Param("id") int id) throws InterruptedException {
        String result = userQueryService.queryUser(id);
        return result;
    }
}
```

重启服务，浏览器输入：http://localhost:8080/query?id=1 返回 “查询成功:User{id=24, username='张三丰', birthday=Fri Jan 01 08:00:00 CST 180, sex='1', address='河南郑州'}”，spring-boot链接mysql成功。

# 10.新增一个主页
新增一个主页，输入id，添加按钮执行查询。右键项目，new --> directory，填入 resources，右键resources --> Mark Directory as --> Resources ROOT。resource中新建index.html，代码如下
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>spring-boot</title>
</head>
<body>
    <h1>spring-boot测试主页</h1>
    <form action="query">
        <input type="text" name="id">
        <input type="submit">
    </form>
</body>
</html>
```
重启服务，浏览器登录：http://localhost:8080/ ，文本框输入 1 ，点击 提交。页面返回：“查询成功:User{id=24, username='张三丰', birthday=Fri Jan 01 08:00:00 CST 180, sex='1', address='河南郑州'}”

本文结束。