### Spring事务角色

----------------

##### 事务角色

- 事务管理员：

  **发起事务方**，在Spring中通常指代业务层开启事务的方法

  在银行转账的案例中，业务层的`transfer`方法就是事务管理员

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
      public void transfer(String out,String in ,Double money);
  }
  
  ```

- 事务协调员：

  **加入事务方**，在Spring中通常指代数据层方法，也可以是业务层方法（业务层在业务层中也可以相互调用）

  在银行转账的案例中，数据层的`outMoney`和`inMoney`就是事务协调员

  ```java
  package com.itheima.dao;
  
  import org.apache.ibatis.annotations.Param;
  import org.apache.ibatis.annotations.Update;
  
  public interface AccountDao {
  
      @Update("update tbl_account set money = money + #{money} where name = #{name}")
      void inMoney(@Param("name") String name, @Param("money") Double money);
  
      @Update("update tbl_account set money = money - #{money} where name = #{name}")
      void outMoney(@Param("name") String name, @Param("money") Double money);
  }
  ```

##### 注意

MyBatis的`SqlSessionFactoryBean`对象用的数据源`DataSource`和JDBC中的事务管理器`PlatformTransactionManager`用的数据源必须保证一致，才可以统一管理