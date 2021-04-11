<h1 align="center">Eureka 的搭建与服务的注册</h1>

## 1. Eureka-Server 的搭建

### Maven 父依赖
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?>
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    &lt;modelVersion>4.0.0&lt;/modelVersion>

    &lt;parent>
        &lt;groupId>org.springframework.boot&lt;/groupId>
        &lt;artifactId>spring-boot-starter-parent&lt;/artifactId>
        &lt;version>1.4.1.RELEASE&lt;/version>
    &lt;/parent>
    &lt;groupId>com.zhiyue&lt;/groupId>
    &lt;artifactId>microservice-spring-cloud&lt;/artifactId>
    &lt;version>1.0-SNAPSHOT&lt;/version>
    &lt;modules>
        &lt;module>microservice-provider&lt;/module>
        &lt;module>microservice-consumer&lt;/module>
        &lt;module>microservice-eureka&lt;/module>
    &lt;/modules>
    &lt;packaging>pom&lt;/packaging>

    &lt;properties>
        &lt;maven.compiler.source>8&lt;/maven.compiler.source>
        &lt;maven.compiler.target>8&lt;/maven.compiler.target>
        <font color="#008b8b">&lt;!-- spring-cloud 版本引入 --></font>
        <font color="#ff1493">&lt;spring.cloud-version>Camden.SR1&lt;/spring.cloud-version></font>
    &lt;/properties>

    <font color="#008b8b">&lt;!-- spring-cloud 版本控制 --></font>
    <font color="#ff69b4">&lt;dependencyManagement>
        &lt;dependencies>
            &lt;dependency>
                &lt;groupId>org.springframework.cloud&lt;/groupId>
                &lt;artifactId>spring-cloud-dependencies&lt;/artifactId>
                &lt;version>${spring.cloud-version}&lt;/version>
                &lt;type>pom&lt;/type>
                &lt;scope>import&lt;/scope>
            &lt;/dependency>
        &lt;/dependencies>
    &lt;/dependencyManagement></font>

    &lt;build>
        &lt;plugins>
            &lt;plugin>
                &lt;groupId>org.springframework.boot&lt;/groupId>
                &lt;artifactId>spring-boot-maven-plugin&lt;/artifactId>
                &lt;version>1.4.1.RELEASE&lt;/version>
            &lt;/plugin>
        &lt;/plugins>
    &lt;/build>

&lt;/project>
</code></pre>

### Maven 依赖

<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?>
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    &lt;parent>
        &lt;artifactId>microservice-spring-cloud&lt;/artifactId>
        &lt;groupId>com.zhiyue&lt;/groupId>
        &lt;version>1.0-SNAPSHOT&lt;/version>
    &lt;/parent>

    &lt;modelVersion>4.0.0&lt;/modelVersion>
    &lt;artifactId>microservice-eureka&lt;/artifactId>

    &lt;properties>
        &lt;maven.compiler.source>8&lt;/maven.compiler.source>
        &lt;maven.compiler.target>8&lt;/maven.compiler.target>
    &lt;/properties>

    &lt;dependencies>
        <font color="#008b8b">&lt;!-- 引入 Spring-Cloud-Eureka 依赖 --></font>
        <font color="#ff69b4">&lt;dependency>
            &lt;groupId>org.springframework.cloud&lt;/groupId>
            &lt;artifactId>spring-cloud-starter-eureka-server&lt;/artifactId>
        &lt;/dependency></font>
    &lt;/dependencies>

&lt;/project>
</code></pre>

### 配置文件 application.yml
<pre><code>server:
  port:  8761

eureka:
  client:
    <font color="darkcyan"># 单机的模拟,通常 Eureka 的 Client 也是一个 Service</font>
    <font color="#ff69b4">register-with-eureka: false</font>
    <font color="#008b8b"># 是否从注册中心拉取注册信息</font>
    <font color="#ff69b4">fetch-registry: false</font>
    service-url:
      <font color="#008b8b"># 默认的注册中心地址健康</font>
      <font color="#ff69b4">defaultZone: http://localhost:8761/eureka</font>
</code></pre>

### 启动类 EurekaApplication.Java
<pre><code>import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
<font color="#008b8b">// 申明当前的服务是个 Eureka 的注册中心服务</font>
<font color="#ff69b4">@EnableEurekaServer</font>
public class EurekaApplication {


    public static void main(String[] args) {
        System.out.println("Hello Eureka!");
        SpringApplication.run(EurekaApplication.class, args);
    }
}
</code></pre>

## 2.Eureka 注册中心开启登录验证
### 在 pom.xml 中加入 spring-boot-starter-security 依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 修改配置文件 application.yml
<pre><code>server:
  port:  8761

eureka:
  client:
    register-with-eureka: false # 单机的模拟,通常 Eureka 的 Client 也是一个 Service
    fetch-registry: false
    service-url:
      defaultZone: http://<font color="#ff69b4">user</font>:<font color="#8a2be2">password</font>@localhost:8761/eureka

<font color="#008b8b"># 配置 Eureka 注册中心的登录验证</font>
<font color="#ff69b4">security:
  basic:
    enabled: true
  user:
    name: user
    password: password</font>
</code></pre>

## 3.将服务注册到 Eureka 注册中心去
### 服务提供者和调用者添加 Maven 依赖
```xml
<!-- Eureka 客户段 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
<!-- 是一个监控管理的生成环境的jar(监控检查) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### 配置文件指定注册中心的地址
<pre><code>server:
  port: 7900
spring:
  jpa:
    generate-ddl: false
    show-sql: true
    hibernate:
      ddl-auto: none
  datasource:
    platform: h2
    schema: classpath:scheam.sql
    data: classpath:data.sql
  # spring.application.name = xx 指定注册中心中的服务名称,建议值全部小写
  application:
    name: microservice-provider
logging:
  level: INFO
  org.hibernate: INFO
  org.hibernate.type.descriptor.sql.BasicBinder: TRACE
  org.hibernate.type.descriptor.sql.BasicException: TRACE
  com.zhiyue: DEBUG
eureka:
  client:
    serviceUrl:
      <font color="#008b8b"># 指定注册中心的地址</font>
      <font color="#ff69b4">defaultZone: http://user:password@localhost:8761/eureka/</font>
    <font color="#008b8b"># 开启健康检查</font>
    healthcheck:
      enable: true

  instance:
    <font color="#008b8b"># 指定注册到注册中心的服务的地址为的前缀为 ip</font>
    <font color="#ff69b4">prefer-ip-address: true</font>
    <font color="#008b8b"># 改变 Eureka 实例的 id</font>
    <font color="#ff69b4">instance-id: ${spring.application.name}:${spring.application.instance_id:${server.port}}</font>
</code></pre>

### 启动类 ProviderApplication.java 注解当前是一个 Eureka 的 client
<pre><code>import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

<font color="#008b8b">/**
 * @EnableDiscoverClient 可以使用其他的服务发现组件(包括eureka)
 */</font>
@SpringBootApplication
<font color="#ff69b4">@EnableEurekaClient</font>
public class ProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(ProviderApplication.class, args);
	}

}
</code></pre>
