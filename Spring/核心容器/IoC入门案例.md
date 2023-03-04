### IoC入门案例

xml版

---------------------

##### 思路分析：

1. 管理什么？

   该案例以Service和Dao为例

2. 如何将被管理的对象告知IoC容器？

   通过自己编写配置（在resource）

3. 被管理的对象交给IoC容器，那么如何获取到IoC容器？

   Spring提供了接口

4. IoC容器得到后，如何从容器中获取bean？

   通过该接口的方法

5. 使用Spring导入哪些坐标？

   用到Spring技术，需要在pom.xml导入坐标

----------------

##### 具体实现：

- 在resource里写配置，在此之前，需要在pom.xml里导入坐标

  ```xml
  <dependency>
  	<groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.10.RELEASE</version>
  </dependency>
  ```

- 定义Spring管理的类（接口），自定义

- 在resource里写配置，文件名applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
      <!--配置bean-->
      <!--bean标签标示配置bean-->
      <!--id属性标示给bean起名字，这样才能找到，注意id不要重复-->
      <!--class属性标示给bean定义类型-->
      <bean id="bookDao" class="org.dao.impl.BookDaoImpl"/>
  
      <bean id="bookService" class="org.service.impl.BookServiceImpl"/>
      
  </beans>
  ```

  通过属性class标识bean对应的是哪个要管理的对象，注意这里要配置实现类

  属性 id 标识，在拿对象的时候通过这个标识得知要拿哪个bean

- 写运行程序，拿IoC容器，拿bean

  ```java
  package org;
  
  import org.dao.BookDao;
  import org.service.BookService;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class App2 {
      public static void main(String[] args) {
          // 获取IoC容器，参数是在resource里写的配置
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
  
          // 获取bean，参数是resource配置里自定义的id，这里的api返回的是一个Object
  //        BookDao bookDao = (BookDao) ctx.getBean("bookDao");
  //        bookDao.save();
  
          BookService bookService = (BookService) ctx.getBean("bookService");
          bookService.save();
      }
  }
  ```

------------

##### 尾巴：

虽然这里实现了IoC的思想，但是实际源代码里还是写着new，并没有做到解耦，这里需要结合DI思想

-----------

##### 案例源码：[GitHub](https://github.com/dddl-z/Spring_Study)

