### bean生命周期

----------

- 生命周期：从创建到消亡的完整过程
- bean生命周期：bean从创建到销毁的整体过程
- bean生命周期控制：在bean创建后到销毁前做一些事情

----------

##### 生命周期控制

- 在bean初始化时做的工作
- 在bean销毁前做的工作

```java
package org.dao.impl;

import org.dao.BookDao;

public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ... success");
    }

    // 表示bean初始化对应的操作
    public void init() {
        System.out.println("book dao init ... success");
    }

    // 表示bean销毁前对应的操作
    public void destroy() {
        System.out.println("book dao destroy ... success");
    }
}
```

##### 如何让Spring知道这两个方法在对应时期执行呢：

配置

```java
<!--init-method表示初始化时做的操作-->
<!--destroy-method表示销毁前做的操作-->
<bean id="bookDao" class="org.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy"/>
```

由于我们的程序都是执行在java虚拟机里，执行完程序虚拟机就会关闭，但是关闭之前并没有对bean进行销毁操作，所以就没有执行destroy

##### 两种方式，使destroy操作执行

- 将获取到的IoC容器关闭，暴力

  ```java
  package org;
  
  import org.dao.BookDao;
  import org.service.Bookservice;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AppForLifeCycle {
      public static void main(String[] args) {
          // 注意这里获取IoC容器的选择的接口不同，因为该接口下有close方法
          ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          BookDao bookDao = (BookDao) ctx.getBean("bookDao");
          bookDao.save();
          ctx.close();
      }
  }
  ```

- 设置关闭钩子

  在容器获取之后，加上一个“钩子”，使得虚拟机在退出之前关闭容器

  ```java
  package org;
  
  import org.dao.BookDao;
  import org.service.Bookservice;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AppForLifeCycle {
      public static void main(String[] args) {
          ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          // 注册关闭钩子
          ctx.registerShutdownHook();
  
          BookDao bookDao = (BookDao) ctx.getBean("bookDao");
          bookDao.save();
  //        ctx.close();
      }
  }
  ```

##### 两种方式的区别：

- `ctx.close()`相对来说比较暴力，只能放在程序末尾
- `ctx.registerShutdownHook()`，比较灵活，写在哪都可以

--------------------------

##### 通过Spring提供的接口进行bean的生命周期控制

```java
package org.service.impl;

import org.dao.BookDao;
import org.service.Bookservice;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

public class BookServiceImpl implements Bookservice, InitializingBean, DisposableBean {

    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ... success");
        bookDao.save();
    }

    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void destroy() throws Exception {
        System.out.println("book service destroy ... success");
    }

    // 当set操作设置完属性之后，才会运行这个方法
    public void afterPropertiesSet() throws Exception {
        System.out.println("book service init ... success");
    }
}
```

:star:注意set方法（设置属性）在初始化之前执行

----------------------

##### bean的生命周期

- 初始化容器
  1. 创建对象（内存分配）
  2. 执行构造方法
  3. 执行属性注入（set操作）
  4. 执行bean初始化方法
- 使用bean
  1. 执行业务操作
- 关闭/销毁容器
  1. 执行bean销毁方法

##### bean销毁时机

- 容器关闭前触发bean的销毁

- 关闭容器的方式

  - 手工关闭容器

    `ClassPathXmlApplicationContext`接口`close()`操作

  - 注册关闭钩子，在虚拟机退出前先关闭容器再退出虚拟机

    `ClassPathXmlApplicationContext`接口`registerShutdownHook()`操作