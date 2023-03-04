### 加载properties文件

-----------

##### 前情提要：

上一节在写配置文件中，发现有一些配置信息是重复使用的

例如上一节在管理Druid和c3p0数据源对象的时候，都用到了相同的mysql驱动类、jdbcUrl、用户名、密码

这些配置信息是重复使用的，如果全部写到配置文件里，当这些配置信息需要改变的时候，可能很多bean的配置都需要改变（牵一发而动全身）

因此，我们可以将这些重复的配置信息单独写道properties文件里

这一节，就来学习如何加载properties文件

---------------------------

##### 需要做什么？

- Spring需要加载这些配置信息组成的文件properties

  1. 修改配置文件，使得Spring可以使用一个全新的命名空间：context

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            
            <!--修改了这里-->    
            xmlns:context="http://www.springframework.org/schema/context"
     
            xsi:schemaLocation="
             http://www.springframework.org/schema/beans
             http://www.springframework.org/schema/beans/spring-beans.xsd
             
             <!--修改了这里-->    
             http://www.springframework.org/schema/context
             http://www.springframework.org/schema/context/spring-context.xsd
             ">
     </beans>
     ```

  2. 使用context命名空间加载properties文件

     ```xml
     <context:property-placeholder location="jdbc.properties"/>
     ```

     properties文件需要自己写

  3. 如果多个properties文件怎么同时加载呢

     可以在location属性里写多个properties文件名，同时使用逗号隔开

     ```xml
     <!--多个properties文件加载-->
     <context:property-placeholder location="jdbc2.properties" system-properties-mode="NEVER"/>
     ```

  4. ##### 更理想的加载多个properties文件的方式

     使用通配符

     ```xml
     <!--使用通配符加载多个properties文件-->
     <context:property-placeholder location="*.properties" system-properties-mode="NEVER"/>
     ```

  5. ##### 最专业的加载多个properties文件的写法

     ```xml
     <!--专业写法-->
     <context:property-placeholder location="classpath:*.properties" system-properties-mode="NEVER"/>
     ```

     以上写法，都只能从当前工程里读取properties文件

  6. 从当前工程依赖的jar包中加载properties文件，并且也可以读取当前工程里的properties文件

     ```java
     <!--读取当前工程依赖的jar包-->
     <context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
     ```

- 将配置文件里的配置信息更改为调用properties文件里的信息

  使用属性占位符`${}`读取properties文件里的属性

  ```xml
  <bean class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driverClassName" value="${jdbc.driver}"/>
      <property name="url" value="${jdbc.url}"/>
      <property name="username" value="${jdbc.username}"/>
      <property name="password" value="${jdbc.password}"/>
  </bean>
  ```

以上各种方式根据需要选择

##### 注意事项：

系统的环境变量值要比properties文件里定义的键值对优先级要高

也就是说，如果properties文件里有一对键值对是：username=dddddl，当我们使用属性占位符读取键值的时候，读取到的是环境变量里username的值

##### 不过也有方法解决这个问题

我们在context加载properties文件的时候，给与属性`system-properties-mode="NEVER"`，就不会加载环境变量里的值了，不加载系统属性

```xml
<context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
```



