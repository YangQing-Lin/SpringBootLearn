### SpringBoot简介（辅助功能之切换web服务器）

---------------

- 辅助功能

  该依赖内置了一个tomcat依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  ```

------------------------

##### SpringBoot程序启动

- 启动方式

  ```java
  package com.example;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  @SpringBootApplication
  public class SpringBoot01QuickstartApplication {
      public static void main(String[] args) {
          SpringApplication.run(SpringBoot01QuickstartApplication.class, args);
      }
  }
  ```

- SpringBoot在创建项目时，**采用jar的打包方式**

- SpringBoot的**引导类**是项目的入口，运行main方法就可以启动项目

##### 使用maven依赖管理变更起步依赖项

比如不想使用tomcat服务器，换成jetty服务器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!--web起步依赖环境中，排除Tomcat起步依赖-->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!--添加Jetty起步依赖，版本由SpringBoot的starter控制-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

Jetty比Tomcat更轻量级，可扩展性更强（相较于Tomcat），谷歌应用引擎（GAE）已经全面切换为Jetty