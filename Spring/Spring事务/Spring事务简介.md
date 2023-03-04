### Spring事务简介

------------

事务作用：在数据层保障一系列的数据库操作同成功同失败

Spring事务：在数据层或业务层保障一系列的数据库操作同成功同失败

##### 如何保障的呢

通过接口

```java
public interface PlatformTransactionManager {
    void commit(TransactionStatus status) throws TransactionException;
    void rollback(TransactionStatus status) throws TransactionException;
}
```

在业务层开一个事务，然后事务里的操作一起提交一起回滚（一起成功一起失败）

并且给出了一个最基本的`PlatformTransactionManager`的实现类

内部是JDBC的事务

```java
public class DataSourceTransactionManager {
    ......
}
```

-----------------------

##### 案例：银行账户转账

模拟银行账户间转账业务

**需求**：实现任意两个账户间转账操作

需求微缩：A账户减钱，B账户加钱

**分析**：

1. 数据层提供基础操作，指定账户减钱（outMoney），指定账户加钱（inMoney）
2. 业务层提供转账操作（transfer），调用减钱与加钱的操作（组合功能得到一个完整的业务）
3. 提供2个账号和操作金额执行转账操作
4. 基于Spring整合MyBatis环境搭建上述操作

##### 结果分析：

1. **程序正常执行时**，账户金额A减B加，没有问题

   在原始案例的环境中，数据层接口提供了`outMoney`和`inMoney`的方法，业务层接口提供了转账的方法

   在业务层实现类中，通过调用数据层接口的两个方法来实现转账的方法

   正常执行的话，可以实现两个账户的之间的转账

2. **程序出现异常后**，转账失败，但是异常之前操作成功，异常之后操作失败，整体业务失败

   业务层接口在实现转账的时候，可能`outMoney`之后，出现了异常，导致程序终止，`inMoney`没有执行，

   导致了资金流失（光减钱，不加钱）

##### 解决的思路

在业务层做成一个完整的事务，让其能够同成功同失败

-----------

##### 思路实现

- 在业务层接口上添加Spring事务管理

  在要加事务的方法上（接口），加注解`@Transactional`，代表在此开启事务

  ```java
  package com.itheima.service;
  
  import org.springframework.transaction.annotation.Transactional;
  
  import java.io.FileNotFoundException;
  import java.io.IOException;
  
  public interface AccountService {
      /**
       * 转账操作
       * @param out 传出方
       * @param in 转入方
       * @param money 金额
       */
      //配置当前接口方法具有事务
      @Transactional
      public void transfer(String out,String in ,Double money) ;
  }
  ```

  **注意**：

  - Spring注解式事务通常添加在业务层接口中而不会添加到业务层实现类中，**降低耦合**
  - 注解式事务可以添加到业务方法上表示当前方法开启事务，**也可以添加到接口上表示当前接口所有方法开启事务**

- 指定事务管理器
  - 在配置中（JDBC配置）配置一个事务管理器`@PlatformTransactionManager`，MyBatis用的是JDBC的事务管理器
  - 并且把这个事务管理器交给Spring管理（bean）
  - 该事务管理器需要JDBC的数据源，使用依赖注入的方式注入数据源
  
  ```java
  package com.itheima.config;
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.jdbc.datasource.DataSourceTransactionManager;
  import org.springframework.transaction.PlatformTransactionManager;
  
  import javax.sql.DataSource;
  
  
  public class JdbcConfig {
      @Value("${jdbc.driver}")
      private String driver;
      @Value("${jdbc.url}")
      private String url;
      @Value("${jdbc.username}")
      private String userName;
      @Value("${jdbc.password}")
      private String password;
  
      @Bean
      public DataSource dataSource(){
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName(driver);
          ds.setUrl(url);
          ds.setUsername(userName);
          ds.setPassword(password);
          return ds;
      }
  
      //配置事务管理器，mybatis使用的是jdbc事务
      @Bean
      public PlatformTransactionManager transactionManager(DataSource dataSource) {
          DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
          transactionManager.setDataSource(dataSource);
          return transactionManager;
      }
  
  
  }
  ```
  
  **注意**
  
  - 事务管理器要根据实现技术进行选择
  - MyBatis框架使用的是JDBC事务，所以使用`DataSourceTransactionManager`实现类
  
- 开启Spring注解式事务驱动，让`@Transactional`生效

  ```java
  package com.itheima.config;
  
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.context.annotation.Import;
  import org.springframework.context.annotation.PropertySource;
  import org.springframework.transaction.annotation.EnableTransactionManagement;
  
  @Configuration
  @ComponentScan("com.itheima")
  @PropertySource("classpath:jdbc.properties")
  @Import({JdbcConfig.class,MybatisConfig.class})
  //开启注解式事务驱动
  @EnableTransactionManagement
  public class SpringConfig {
  }
  ```

------------

##### 预期

当事务中发生异常后，会同失败，一起回滚初始