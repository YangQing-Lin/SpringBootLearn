### bean配置

-------------

##### bean基础配置：

| 类别     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| 名称     | bean                                                         |
| 类型     | 标签                                                         |
| 所属     | beans标签                                                    |
| 功能     | 定义Spring核心容器管理的对象                                 |
| 格式     | <beans>                                                                                                                                                                                             <bean/>                                                                                                                                                                                                   <bean></bean>                                                                                                                                                                         </beans> |
| 属性列表 | id：bean的id，使用容器可以通过id值获取对应的bean，在一个容器中id值唯一                                                                    class：bean的类型，即配置的bean的全路径类名 |
| 范例     | ```<bean id="bookDao" class="org.dao.impl.BookDaoImpl"/>```  |

-----------

##### bean别名配置：

| 类别 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| 名称 | name                                                         |
| 类型 | 属性                                                         |
| 所属 | bean标签                                                     |
| 功能 | 定义bean的别名，可以定义多个，使用逗号分号空格都可以进行分隔 |
| 范例 | 如下                                                         |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--配置bean-->
    <!--bean标签标示配置bean-->
    <!--id属性标示给bean起名字，这样才能找到-->
    <!--class属性标示给bean定义类型-->
    <!--name里是别名，任意多个-->
    <bean id="bookDao" name="dao" class="org.dao.impl.BookDaoImpl"/>

    <!--name里是别名，任意多个-->
    <bean id="bookService" name="service" class="org.service.impl.BookServiceImpl">
        <!--配置service与dao的关系-->
        <!--name就是service里定义的dao的变量，ref是需要的bean的id-->
        <!--property标签表示配置当前bean的属性-->
        <!--name属性表示配置哪一个具体的属性-->
        <!--ref属性表示参照哪一个bean，可以参照对应bean的id或者name，一般是id-->
        <property name="bookDao" ref="bookDao"/>
    </bean>
    
</beans>
```

##### 注意：

获取bean无论是通过id还是name获取，如果无法获取到，将抛出一个异常：`NoSuchBeanDefinitionException`

------------

##### bean的作用范围：

作用范围是什么呢：

- 创建的bean是一个对象还是多个对象，即造的对象是单例的还是非单例的

  Spring默认创建的bean是单例的

  例如：以下代码打印出的两个对象的地址是一样的

  ```java
  package org;
  
  import org.dao.BookDao;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AppForScope {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          BookDao bookDao1 = (BookDao) ctx.getBean("bookDao");
          BookDao bookDao2 = (BookDao) ctx.getBean("bookDao");
          System.out.println(bookDao1); // 输出该对象的所属实现类的完整路径和其地址
          System.out.println(bookDao2);
      }
  }
  ```

##### 通过配置造一个非单例的bean：

```xml
<bean id="bookDao" name="dao" class="org.dao.impl.BookDaoImpl" scope="prototype"/>
<!--scope="prototype"-->
```

| 类别 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| 名称 | scope                                                        |
| 类型 | 属性                                                         |
| 所属 | bean标签                                                     |
| 功能 | 定义bean的作用范围，可选范围如下：                                                                                                                                 singleton：单例（默认）                                                                                                                                                       prototype：非单例 |
| 范例 | ```<bean id="bookDao" name="dao" class="org.dao.impl.BookDaoImpl" scope="prototype"/>``` |

##### 为什么bean默认为单例的？

- 对于Spring来说，其帮我们管理的bean需要放在容器中

  如果在有一个场景：如果其造出来的bean是非单例的，那么该bean的数量会有无穷多个（用一次造一个）

  所以Spring并不是帮我们管理这一类bean的，这样对于Spring容器来说压力也很大

- Spring默认管理的bean是单例的，会对我们的业务产生影响吗？

  不会，造对象是为了执行对应的方法，是可以复用的

- Spring在帮我们管理对象的时候，其实就是管理那些可以复用的对象

##### 适合交给IoC容器进行管理的bean

- 表现层对象
- 业务层对象
- 数据层对象
- 工具对象

##### 不适合交给容器进行管理的bean

这些对象是有状态的

- 封装实体的域对象

---------

##### 个人理解：

IoC容器里的bean相当于是一个类，单例指的是该类只有一个实例，且该实例会被复用

----------

##### 尾巴：

单例bean是如何造出来的，也是用new实现吗

