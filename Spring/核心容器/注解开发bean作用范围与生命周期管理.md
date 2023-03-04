### 注解开发bean作用范围与生命周期管理

--------------

##### bean作用范围

- 默认为单例，也可以设置为单例，在实现类前加上注解`@Scope("singleton")`

  ```java
  package org.dao.impl;
  
  import org.dao.BookDao;
  import org.springframework.context.annotation.Scope;
  import org.springframework.stereotype.Repository;
  
  @Repository
  @Scope("singleton")
  public class BookDaoImpl implements BookDao {
      public void save() {
          System.out.println("book dao save ... success");
      }
  }
  ```

- 控制为非单例对象，在实现类前加上注解`@Scope("prototype")`

  ```java
  package org.dao.impl;
  
  import org.dao.BookDao;
  import org.springframework.context.annotation.Scope;
  import org.springframework.stereotype.Repository;
  
  @Repository
  @Scope("prototype")
  public class BookDaoImpl implements BookDao {
      public void save() {
          System.out.println("book dao save ... success");
      }
  }
  ```

---------------

##### bean生命周期管理

由于没有使用配置文件，所以可以考虑使用Spring提供的接口`InitializingBean, DisposableBean`

不过目前学习的是纯注解的方式

- 先写出生命周期方法：`init()`和`destroy()`

- 添加对应注解

  ```java
  package org.dao.impl;
  
  import org.dao.BookDao;
  import org.springframework.beans.factory.DisposableBean;
  import org.springframework.beans.factory.InitializingBean;
  import org.springframework.context.annotation.Scope;
  import org.springframework.stereotype.Repository;
  
  import javax.annotation.PostConstruct;
  import javax.annotation.PreDestroy;
  
  @Repository
  @Scope("singleton")
  public class BookDaoImpl implements BookDao {
      public void save() {
          System.out.println("book dao save ... success");
      }
  
      // 生命周期初始化方法
      @PostConstruct
      public void init() {
          System.out.println("book dao init ... success");
      }
  
      // 生命周期销毁方法
      @PreDestroy
      public void destroy() {
          System.out.println("book dao destroy ... success");
      }
  }
  ```

  `PostConstruct`，初始化在构造方法后执行

  `PreDestroy`，销毁方法在销毁之前执行

初始化方法一般自动执行，销毁方法，需要在容器关闭时执行

- 手动关闭

  ```java
  package org;
  
  import org.config.SpringConfig;
  import org.dao.BookDao;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.annotation.AnnotationConfigApplicationContext;
  
  public class App {
      public static void main(String[] args) {
          AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
          BookDao bookDao1 = ctx.getBean(BookDao.class);
          BookDao bookDao2 = ctx.getBean(BookDao.class);
          System.out.println(bookDao1);
          System.out.println(bookDao2);
          ctx.close();
      }
  }
  ```

- 注册关闭钩子

  ```java
  package org;
  
  import org.config.SpringConfig;
  import org.dao.BookDao;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.annotation.AnnotationConfigApplicationContext;
  
  public class App {
      public static void main(String[] args) {
          AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
          BookDao bookDao1 = ctx.getBean(BookDao.class);
          BookDao bookDao2 = ctx.getBean(BookDao.class);
          System.out.println(bookDao1);
          System.out.println(bookDao2);
          ctx.registerShutdownHook();
      }
  }
  ```

##### 有个很奇怪的点？

当设置为非单例的时候，使用以上两种方法关闭容器都不会触发销毁方法

