### SSM整合（接口测试）

------------------

##### SSM整合流程

1. 创建工程
2. ssm整合
   - Spring
     - SpringConfig
   - MyBatis
     - MybatisConfig
     - JdbcConfig
     - jdbc.properties
   - SpringMVC
     - ServletConfig
     - SpringMvcConfig
3. 功能模块
   - 表与实体类
   - dao（接口+自动代理）
   - service（接口+实现类）
     - 业务层接口测试（整合JUnit）
   - controller
     - 表现层接口测试（PostMan）
   - 事务处理

----------------

##### 接口测试

- 业务层接口测试（整合JUnit）

  - 设置测试类`@RunWith(SpringJUnit4ClassRunner.class)`
  - 设置测试环境`@ContextConfiguration(classes = SpringConfig.class)`
  - 编写测试方法
  - 直接测试

  ```java
  package org.service;
  
  import org.config.SpringConfig;
  import org.domain.Book;
  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.test.context.ContextConfiguration;
  import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
  
  import java.util.List;
  
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(classes = SpringConfig.class)
  public class BookServiceTest {
  
      @Autowired
      private BookService bookService;
  
      @Test
      public void testGetById() {
          Book book = bookService.getById(1);
          System.out.println(book);
      }
  
      @Test
      public void testGetAll() {
          List<Book> bookList = bookService.getAll();
          System.out.println(bookList);
      }
  }
  ```

- 表现层（控制层）接口测试

  使用postman测试

  img

----------------------

##### 事务处理

- 开启注解式事务驱动

  在SpringConfig上添加注解`@EnableTransactionManagement`

- 配置事务的管理器

  在JdbcConfig里写事务管理器

  ```java
  @Bean
  public PlatformTransactionManager transactionManager(DataSource dataSource) {
      DataSourceTransactionManager ds = new DataSourceTransactionManager();
      ds.setDataSource(dataSource);
      return ds;
  }
  ```

- 添加事务，`@Transactional`配到接口上

  在Service接口上添加注解

--------------------

##### 总结

- Spring整合MyBatis
  - 配置
    - SpringConfig
    - JDBCConfig，jdbc.properties
    - MyBatisConfig
  - 模型
    - Book
  - 数据层标准开发
    - BookDao
  - 业务层标准开发
    - BookService
    - BookServiceImpl
  - 测试接口
    - BookServiceTest
  - 事务处理
- Spring整合SpringMVC
  - web配置类
  - SpringMVC配置类
  - 基于Restful的Controller开发