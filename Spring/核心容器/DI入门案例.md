### DI入门案例

xml版

---------------

##### 思路分析：

1. 基于IoC管理bean

2. 思考Service中使用new形式创建的Dao对象是否保留？

   答案是否，使用new创建Dao对象，耦合度偏高

3. Service中需要的Dao对象如何放入到Service中？

   提供方法，该方法可以从外部将Dao对象放入Service中

4. Service与Dao间的关系如何描述？

   配置

-----------

##### 具体实现：

- 删除业务层中使用new形式创建的Dao对象

  ```java
      // 删除业务层中使用new的方式创建的Dao对象
  //    private BookDao bookDao = new BookDaoImpl();
  ```

- 将业务层中需要的Dao对象放入业务层，需要使用set方法（**提供依赖对象对应的Set方法，该Set方法是IoC容器在执行**）

  ```java
  // 提供对应的set方法
  public void setBookDao(BookDao bookDao) {
      this.bookDao = bookDao;
  }
  ```

- 通过配置（自己写）告诉set应该放入什么对象，即将依赖关系放入IoC容器（DI依赖注入）

  既然要在service里放入dao对象，则在service对应的bean标签里配置

  ```java
  <bean id="bookDao" class="org.dao.impl.BookDaoImpl"/>
  
  <bean id="bookService" class="org.service.impl.BookServiceImpl">
      <!--配置service与dao的关系-->
      <!--name就是service里定义的dao的变量，ref是需要的bean（当前容器中）的id-->
      <!--property标签表示配置当前bean的属性-->
      <!--name属性表示配置哪一个具体的属性-->
      <!--ref属性表示参照哪一个bean-->
      <property name="bookDao" ref="bookDao"/>
  </bean>
  ```

