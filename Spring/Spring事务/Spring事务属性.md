### Spring事务属性

-------------

##### 事务的相关配置

这些配置项都可以在注解`@Transactional`上开启

| 属性                   | 作用                                          | 示例                                       |
| ---------------------- | --------------------------------------------- | ------------------------------------------ |
| readOnly               | 设置是否为只读事务    默认是false，即读写事务 | readOnly=true   只读事务                   |
| timeout                | 设置事务超时时间                              | timeout = -1    永不超时                   |
| rollbackFor            | 设置事务回滚异常（class）                     | rollbackFor = {NullPointException.class}   |
| rollbackForClassName   | 设置事务回滚异常（String）                    | 同上格式为字符串                           |
| noRollbackFor          | 设置事务不回滚异常（class）                   | noRollbackFor = {NullPointException.class} |
| noRollbackForClassName | 设置事务不回滚异常（String）                  | 同上格式为字符串                           |
| propagation            | 设置事务传播行为                              | ......                                     |

##### 程序中示例

- readOnly

  ```java
  @Transactional(readOnly = true)
  public void transfer(String out,String in ,Double money) throws IOException;tt
  ```

- timeout

  ```java
  @Transactional(readOnly = true, timeout = -1)
  public void transfer(String out,String in ,Double money) throws IOException;
  ```

- rollbackFor

  指定遇到某一类异常就回滚事务

  **因为事务遇到某些异常是不回滚的**

  - 例如，在遇到`IOException`是没有回滚的

  - 如果我们的程序中出现的异常属于以下两种，就会做事务回滚

    - error
    - 运行时异常`RunningtimeException`，比如`NullPointException`

    **除此之外，都不回滚**

  在这种情况下，需要设置`rollbackFor = Exception`，遇到了指定的`Exception`就回滚

  ```java
  @Transactional(rollbackFor = {IOException.class})
  public void transfer(String out,String in ,Double money) throws IOException;
  ```

- rollbackForClassName、noRollbackFor、noRollbackForClassName与上述类似

- **事务传播行为**：事务协调员对事务管理员所携带的事务的处理态度

  处理态度：加入事务管理员还是不加入，又或者是自己单开

-------------

##### 案例：转账业务追加日志

**需求**：实现任意两个账户间的转账操作，并对每次转账操作在数据库进行留痕

**需求微缩**：A账户减钱，B账户加钱，数据库记录日志

**分析**：

1. 基于转账操作案例**添加日志模块**，实现数据库中记录日志
2. 业务层转账操作（transfer），调用减钱、加钱与记录日志功能

**实现效果预期**：无论转账操作是否成功，均进行转账操作的日志留痕

##### 实现

- 在数据层增加log模块，该模块实现了在数据库里添加转账记录的功能

- 在业务层增加log模块，调用数据层log

- 在业务层的转账方法`transfer`中添加记录日志的功能

  - 通过引入业务层log模块的方式调用记录日期的功能
  - 使用`try finally`保证记录日志的功能一定执行

- 在业务层的`transfer`方法上加`@Transaction`，同样的，业务层的log模块的log方法也需要加`@Transaction`，因为调用了数据层的log方法

  但是最后**业务层的log方法事务也并入了业务层的`transfer`事务**

**存在问题**：日志的记录与转账操作隶属于同一个事务，同成功同失败，与预期的无论成功与否都进行日志的记录效果不符合

**实现效果预期改进**：无论转账操作是否成功，日志必须保留

**改进思路**：日志的记录事务不要加入转账的事务中，单开

**改进实现**

- 通过配置**事务的传播行为**实现日志事务的单开

- 在业务层的日志模块log方法上的`@Transaction`注解设置事务的传播行为`REQUIRES_NEW`（需要新事务）

  ```java
  package com.itheima.service;
  
  import org.springframework.transaction.annotation.Propagation;
  import org.springframework.transaction.annotation.Transactional;
  
  public interface LogService {
      //propagation设置事务属性：传播行为设置为当前操作需要新事务
      @Transactional(propagation = Propagation.REQUIRES_NEW)
      void log(String out, String in, Double money);
  }
  ```

--------------

##### 事务传播行为

img