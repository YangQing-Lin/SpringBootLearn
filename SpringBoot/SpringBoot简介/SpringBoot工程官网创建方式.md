### SpringBoot工程官网创建方式

-------------

##### 入门案例

- 最简SpringBoot程序所包含的基础文件

  - pom.xml文件

    ```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    ```

    创建的时候选择的技术集

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

  - Application类

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

---------------

##### Spring程序与SpringBoot程序对比

|      类/配置文件       |  Spring  | SpringBoot |
| :--------------------: | :------: | :--------: |
|    pom文件中的坐标     | 手工添加 |  勾选添加  |
|      web3.0配置类      | 手工制作 |     无     |
| Spring/SpringMVC配置类 | 手工制作 |     无     |
|         控制器         | 手工制作 |  手工制作  |

**注意：**基于idea开发SpringBoot程序需要确保联网且能够加载到程序框架结构

-------------

##### 官网创建

img