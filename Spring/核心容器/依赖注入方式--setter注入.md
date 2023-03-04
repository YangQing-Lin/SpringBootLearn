### 依赖注入方式

setter注入

---------

##### 思考：向一个类中传递数据的方式有几种？

- 普通方法（set方法）
- 构造方法

即依赖注入的时候，除了使用set方法传，还可以使用构造方法传

##### 思考：依赖注入描述了在容器中建立bean与bean之间依赖关系的过程，如果bean运行需要的是数字或字符串呢？

则注入的数据分为两种

- 引用类型
- 简单类型（基本数据类型与String）

##### 那么依赖注入方式分为四种：

- ##### setter注入

  - 简单类型
  - 引用类型（之前讲过）

- ##### 构造器注入

  - 简单类型
  - 引用类型

-------------

##### setter注入---引用类型

- 在bean中定义引用类型属性并提供可访问的set方法

  ```java
  private BookDao bookDao;
  
  public void setBookDao(BookDao bookDao) {
      this.bookDao = bookDao;
  }
  ```

- 配置中使用property标签ref属性注入引用类型对象

  ```java
  <bean id="bookService" class="org.service.impl.BookServiceImpl">
      <property name="bookDao" ref="bookDao"/>
  </bean>
  ```

- 如果有多个，就重复上述操作

##### setter注入---简单类型

与引用类型相似

- 在bean中定义简单类型属性并提供可访问的set方法

  ```java
  package org.dao.impl;
  
  import org.dao.BookDao;
  
  public class BookDaoImpl implements BookDao {
  
      private int connectionNum;
      private String databaseName;
  
      public void setConnectionNum(int connectionNum) {
          this.connectionNum = connectionNum;
      }
  
      public void setDatabaseName(String databaseName) {
          this.databaseName = databaseName;
      }
  
      public void save() {
          System.out.println("book dao save ... success" 
                  + "connectionNum = " + connectionNum
          + "databaseName" + databaseName);
      }
  }
  ```

- 配置中使用property标签value属性注入简单类型对象

  ```java
  <bean id="bookDao" class="org.dao.impl.BookDaoImpl">
      <property name="connectionNum" value="10"/>
      <property name="databaseName" value="mysql"/>
  </bean>
  ```

- 如果有多个，就重复上述操作

setter注入很灵活