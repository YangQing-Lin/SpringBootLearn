### SpringMVC入门案例

------------

##### 准备

创建一个Maven工程，骨架为webapp，将源码目录更改为java

------

##### 步骤

- 使用SpringMVC技术需要先导入SpringMVC坐标与Servlet坐标

  ```xml
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope> <!--防止与tomcat插件冲突-->
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE </version>
    </dependency>
  </dependencies>
  ```

- 创建SpringMVC控制器类（等同于Servlet功能）

  - 先将控制器类定义成Spring的bean，加注解`@Controller`
  - 编写处理请求的方法

    - 返回json
  - 设置该方法的访问路径，为方法加注解`@RequestMapping("/save")`
    - 设置该方法的返回值类型，Json类型加注解`@ResponseBody`，该注解表示将返回值整体作为相应内容

  ```java
  package org.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  
  @Controller//定义spring管理的bean
  public class UserController {
  
      @RequestMapping("/save") // 定义当前操作的访问路径
      @ResponseBody
      public String save() {
          System.out.println("user controller save ... success");
          return "{'module' : 'springmvc'}";
      }
  }
  ```

- 初始化SpringMVC环境（同Spring环境），设定SpringMVC加载对应的bean

  相当于做Spring的配置类

  ```java
  package org.config;
  
  
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  
  @Configuration
  @ComponentScan("org.controller")
  public class SpringMvcConfig {
  }
  ```

- 初始化Servlet容器，加载SpringMVC环境，并设置SpringMVC技术处理的请求

  编写配置类：使服务器启动时加载SpringMVC配置

  **编写规范**

  按照Spring容器支持的格式开发

  - 继承`AbstractDispatcherServletInitializer`抽象类

  - 覆盖方法`createServletApplicationContext`

    加载SpringMvc容器配置

  - 覆盖方法`getServletMappings`

    设置哪些请求归属SpringMvc处理

  - 覆盖方法`createRootApplicationContext`

    加载Spring容器配置

  ```java
  package org.config;
  
  import org.springframework.web.context.WebApplicationContext;
  import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
  import org.springframework.web.servlet.support.AbstractDispatcherServletInitializer;
  
  // 定义一个servlet容器启动的配置类，在里面加载spring的配置
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
      protected WebApplicationContext createServletApplicationContext() {
          // 专用于web环境
          AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
          ctx.register(SpringMvcConfig.class); // 加载配置
          return ctx;
      }
  
      protected String[] getServletMappings() {
          return new String[]{"/"}; // 所有请求
      }
  
      protected WebApplicationContext createRootApplicationContext() {
          return null; // 暂时没有spring容器，可以先不用管
      }
  }
  ```

- 配置Tomcat插件

  在pom.xml文件里

  ```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path> <!--这里配置/，就不用在请求链接里加上项目名了-->
        </configuration>
      </plugin>
    </plugins>
  </build>
  ```

  设置运行配置`conmmond line`为tomcat
  

---------------

##### 注解

| 名称           | 类型     | 位置                        | 作用                                             | 相关属性                    |
| -------------- | -------- | --------------------------- | ------------------------------------------------ | --------------------------- |
| @Controller    | 类注解   | SpringMVC控制器类定义上方   | 设定SpringMVC的核心控制器bean                    |                             |
| @RequestMapper | 方法注解 | SpringMVC控制器方法定义上方 | 设置当前控制器方法请求访问路径                   | value（默认）：请求访问路径 |
| @ResponseBody  | 方法注解 | SpringMVC控制器方法定义上方 | 设置当前控制器方法响应内容为当前返回值，无需解析 |                             |

----------------

##### SpringMVC入门程序开发总结（1 + N）

- 一次性工作
  - 创建工程，设置服务器，加载工程
  - 导入坐标
  - 创建web容器启动类，加载SpringMVC配置，并设置SpringMVC请求拦截路径
  - SpringMVC核心配置类（设置配置类，扫描controller包，加载Controller控制器bean）
- 多次工作
  - 定义处理请求的控制器类
  - 定义处理请求的控制器方法，并配置映射路径（@RequestMapping）与返回json数据（@ResponseBody）

----------------

##### 关于SpringMVCweb容器的启动类

这种写法是SpringMVC专用

- `AbstractDispatcherServletInitializer`类是SpringMVC提供的快速初始化Web3.0容器的抽象类

- `AbstractDispatcherServletInitializer`提供了三个接口方法供用户实现

  - `createServletApplicationContext()`方法，创建Servlet容器时，加载SpringMVC对应的bean并放入

    `WebApplicationContext`对象范围中，而`WebApplicationContext`的作用范围为`ServletContext`范围，即整个web容器范围

    ```java
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        ctx.register(SpringMvcConfig.class);
        return ctx;
    }
    ```

  - `getServletMappings()`方法，设定SpringMVC对应的请求映射路径，设置为/表示拦截所有请求，任意请求都将转入到SpringMVC进行处理

    ```java
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
    ```

  - `createRootApplicationContext()`方法，如果创建Servlet容器时需要加载非SpringMVC对应的bean，使用当前方法进行，使用方式同`createServletApplicationContext()`

    ```java
    protected WebApplicationContext createRootApplicationContext() {
        return null; // 暂时没有spring容器，可以先不用管
    }
    ```