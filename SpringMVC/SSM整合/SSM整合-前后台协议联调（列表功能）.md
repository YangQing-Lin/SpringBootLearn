### SSM整合-前后台协议联调（列表功能）

---------------------

##### 复习

将静态资源文件粘贴到webapp包下

SpringMVC设定了拦截所有的请求，页面的请求也会拦截 

- **编写`SpringMvcSupport`类**

  对静态资源的访问指定到静态文件（不拦截）

  ```java
  package org.config;
  
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
  
  @Configuration
  public class SpringMvcSupport extends WebMvcConfigurationSupport {
  
      @Override
      protected void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
          registry.addResourceHandler("/css/**").addResourceLocations("/css/");
          registry.addResourceHandler("/js/**").addResourceLocations("/js/");
          registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
      }
  }
  ```

- 要使该配置类被SpringMvc的配置类扫描到

  **不要使用`@Import`注解引入配置类**

  使用`@Configuration`，然后用`@ComponentScan`扫描包

  ```java
  package org.config;
  
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.EnableWebMvc;
  
  
  @ComponentScan({"org.controller", "org.config"})
  @EnableWebMvc
  public class SpringMvcConfig {
  }
  ```

-------------------

##### 调通list（books.html）页面功能

列表功能

```js
//列表
getAll() {
    // 发送Ajax请求
    axios.get("/books").then((res)=>{
        this.dataList = res.data.data;
    });
},
```

