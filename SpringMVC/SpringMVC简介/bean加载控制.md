### bean加载控制

-------------

##### Controller加载控制与业务bean加载控制

- SpringMVC相关bean（表现层bean）
- Spring控制的bean
  - 业务bean（Service）
  - 功能bean（DataSource等）

##### 因为功能不同，如何避免Spring错误的加载到SpringMVC的bean？

加载Spring控制的bean时排除掉SpringMVC控制的bean

##### 具体做法

- SpringMVC相关bean加载控制

  - SpringMVC加载的bean对应的包均在org.controller包内

- Spring相关bean加载控制

  - 方式一：Spring加载的bean设定扫描范围为org，排除掉controller包内的bean

    ```java
    package com.itheima.config;
    
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.FilterType;
    import org.springframework.stereotype.Controller;
    
    @Configuration
    //@ComponentScan({"com.itheima.service","com.itheima.dao"})
    //设置spring配置类加载bean时的过滤规则，当前要求排除掉表现层对应的bean
    //excludeFilters属性：设置扫描加载bean时，排除的过滤规则
    //type属性：设置排除规则，当前使用按照bean定义时的注解类型进行排除
    //classes属性：设置排除的具体注解类，当前设置排除@Controller定义的bean
    @ComponentScan(value = "com.itheima",
            excludeFilters = @ComponentScan.Filter(
                    type = FilterType.ANNOTATION,
                    classes = Controller.class
            ))
    public class SpringConfig {
    }
    ```

  - 方式二：Spring加载的bean设定扫描范围为精准范围，例如service包，dao包等

    ```java
    package com.itheima.config;
    
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    @ComponentScan({"com.itheima.service", "com.itheima.dao"})
    public class SpringConfig {
    }
    ```

  - 方式三：不区分Spring与SpringMVC的环境，加载到同一个环境中

-----------

##### 注意

如果某一个类上有注解`@Configruation`，那么SpringConfig在扫描的时候，也会扫描这个类

所以要想Spring排除SpringMVC的bean，不仅要设置`excludeFilters`，还要将SpringMVC的注解`@ComponentScan`删除

-----------------

##### 加载Spring的配置类

在服务器容器启动类里，将Spring的配置类加载上

```java
protected WebApplicationContext createRootApplicationContext() {
    AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
    ctx.register(SpringConfig.class);
    return ctx;
}
```

-------------

##### 注解

- `@ComponentScan`

  属性

  - `excludeFilters`：排除扫描路径中加载的bean，需要指定类别（type）与具体项（classes）
  - `includeFilters`：加载指定的bean，需要指定类别（type）与具体项（classes）

------------------

##### 简化bean的加载格式

原始

```java
public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        ctx.register(SpringMvcConfig.class);
        return ctx;
    }
    protected WebApplicationContext createRootApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        ctx.register(SpringConfig.class);
        return ctx;
    }
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

}
```

简化

```java
//web配置类简化开发，仅设置配置类类名即可
public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {

    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```