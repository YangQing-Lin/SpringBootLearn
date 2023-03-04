### bean实例化

-----

##### bean本质上就是对象，创建bean使用构造方法完成

```java
public BookDaoImpl() {
    System.out.println("book dao constructor is running ... success");
}

// 该构造方法在bean的对象创建的时候也会使用
// 且不管该构造方法是公共的还是私有的
// 如果是私有的，那么使用了反射机制
```

Spring创建bean的时候调用的是**无参的构造方法**

如果没有提供无参的构造方法（如果自己构造就使用自己写的，如果没有，系统自动分配），将抛出异常：BeanCreationException

##### :star2:spring的报错如何阅读呢

- 拉到最后，查看最后一个异常信息
- 如果处理了最后一个异常信息能够解决问题，则ok，如果不能，就往上再解决倒数第二个
- 以此类推，依次往上解决异常信息

---------------------------

##### 使用静态工厂实例化bean：

静态工厂：

```java
package org.factory;

import org.dao.OrderDao;
import org.dao.impl.OrderDaoImpl;

public class OrderDaoFactory {
    public static OrderDao getOrderDao() {
        return new OrderDaoImpl();
    }
}
```

使用静态工厂造对象：

```java
package org;

import org.dao.OrderDao;
import org.factory.OrderDaoFactory;

public class AppForInstanceOrder {
    public static void main(String[] args) {
        OrderDao orderDao = OrderDaoFactory.getOrderDao();
        orderDao.save();
    }
}
```

以上是早些年写程序的方式，不主动使用new造对象，而是使用静态工厂，一定程度上解耦

##### 如果我们的对象是使用这样的方式造出来的，交给Spring管理该如何管理呢

- 配置

  ```java
  <!--使用静态工厂实例化bean-->
  <!--class配的是工厂类名-->
  <!--属性factory-method表示使用该工厂的哪个方法造对象-->
  <bean id="orderDao" class="org.factory.OrderDaoFactory" factory-method="getOrderDao"/>
  ```

- 使用IoC容器及其方法得到对象

  ```java
  package org;
  
  import org.dao.OrderDao;
  import org.factory.OrderDaoFactory;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AppForInstanceOrder {
      public static void main(String[] args) {
          // 使用静态工厂早对象
  //        OrderDao orderDao = OrderDaoFactory.getOrderDao();
  //        orderDao.save();
  
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          OrderDao orderDao = (OrderDao) ctx.getBean("orderDao");
          orderDao.save();
      }
  }
  ```

--------------------

##### 实例化工厂

- 实例工厂

  ```java
  package org.factory;
  
  import org.dao.UserDao;
  import org.dao.impl.UserDaoImpl;
  
  public class UserDaoFactory {
      // 不是静态的
      public UserDao getUserDao() {
          return new UserDaoImpl();
      }
  }
  ```

- 使用实例工厂创建对象

  ```java
  package org;
  
  import org.dao.UserDao;
  import org.factory.UserDaoFactory;
  
  public class AppForInstanceUser {
      public static void main(String[] args) {
          // 创建实例工厂对象
          UserDaoFactory userDaoFactory = new UserDaoFactory();
          // 通过实例工厂对象创建对象
          UserDao userDao = userDaoFactory.getUserDao();
          userDao.save();
      }
  }
  ```

同样，这是早期写程序的一种方式

##### 现在，我们如何通过Spring管理这种方式造出来的对象呢

- 配置

  ```xml
  <!--使用实例工厂实例化bean-->
  <!--先将实例工厂的bean造出来-->
  <!--factory-bean表示使用哪个实例工厂的bean-->
  <!--factory-method表示使用该工厂的哪个方法造对象-->
  <bean id="userFactory" class="org.factory.UserDaoFactory"/>
  <bean id="userDao" factory-bean="userFactory" factory-method="getUserDao"/>
  ```

- Spring方式造对象

  ```java
  package org;
  
  import org.dao.UserDao;
  import org.factory.UserDaoFactory;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AppForInstanceUser {
      public static void main(String[] args) {
          // 创建实例工厂对象
  //        UserDaoFactory userDaoFactory = new UserDaoFactory();
          // 通过实例工厂对象创建对象
  //        UserDao userDao = userDaoFactory.getUserDao();
  //        userDao.save();
  
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserDao userDao = (UserDao) ctx.getBean("userDao");
          userDao.save();
      }
  }
  ```

##### 发现弊端：

- 可以发现，实例工厂的bean完全是为了配合其他bean使用的，实际没有意义
- `factory-method`方法名不固定，每次都需要配置

##### Spring针对这些问题，对第三种方式（实例工厂）做出了改良：FactoryBean

不需要实例工厂的bean，且造对象的方法（`factory-method`）固定

- ##### FactoryBean

  ```java
  package org.factory;
  
  import org.dao.UserDao;
  import org.dao.impl.UserDaoImpl;
  import org.springframework.beans.factory.FactoryBean;
  
  public class UserDaoFactoryBean implements FactoryBean<UserDao> {
      // 代替原始实例工厂中创建对象的方法
      public UserDao getObject() throws Exception {
          return new UserDaoImpl();
      }
  
      public Class<?> getObjectType() {
          return UserDao.class;
      }
  }
  ```

- 配置

  ```java
  <!--使用FactoryBean实例化-->
  <bean id="userDao" class="org.factory.UserDaoFactoryBean"/>
  ```

- 默认创建对象是单例的，可以通过覆盖`FactoryBean`的方法`isSingleton()`，改变是否单例

  ```java
  package org.factory;
  
  import org.dao.UserDao;
  import org.dao.impl.UserDaoImpl;
  import org.springframework.beans.factory.FactoryBean;
  
  public class UserDaoFactoryBean implements FactoryBean<UserDao> {
      // 代替原始实例工厂中创建对象的方法
      public UserDao getObject() throws Exception {
          return new UserDaoImpl();
      }
  
      public Class<?> getObjectType() {
          return UserDao.class;
      }
  
      // 表示创建的对象是单例的还是非单例的，true表示单例，false表示非单例的
      public boolean isSingleton() {
          return false;
      }
  }
  ```

  



