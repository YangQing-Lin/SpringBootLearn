### 注解开发管理第三方bean

-------------

##### 第三方bean管理

由于不能直接写在第三方的源代码中，所以这里只能自己编程

先导入数据源对象的坐标

在配置类里

- 定义一个方法，获取要管理的对象

  这里方法名就定义成bean的id（自定义）

  ```java
  // 定义一个方法获取要管理的对象
  public DataSource dataSource() {
      DruidDataSource ds = new DruidDataSource();
      return ds;
  }
  ```

  同时要设置对象的属性

  ```java
  package org.config;
  
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.context.annotation.Configuration;
  
  import javax.sql.DataSource;
  
  @Configuration
  public class SpringConfig {
      // 定义一个方法获取要管理的对象
      public DataSource dataSource() {
          // 这里不能写接口
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName("com.mysql.jdbc.Driver");
          ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
          ds.setUsername("root");
          ds.setPassword("root");
          return ds;
      }
  }
  ```

- 使用`@Bean`配置第三方bean

  将该方法的返回值定义成bean，在该方法的上面加上注释`@Bean`

  如果想对该bean起名，可以`@Bean("name")`，一般不写
  
  ```java
  package org.config;
  
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  
  import javax.sql.DataSource;
  
  @Configuration
  public class SpringConfig {
      // 定义一个方法获取要管理的对象
      @Bean
      public DataSource dataSource() {
          // 这里不能写接口
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName("com.mysql.jdbc.Driver");
          ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
          ds.setUsername("root");
          ds.setPassword("root");
          return ds;
      }
  }
  ```

如果想管理其他的bean，直接照样子在配置类里写方法即可

##### 拆分配置

`SpringConfig`是spring的配置类，`dataSourse`是关于jdbc的配置

新建一个配置类`JdbcConfig`

- 扫描式

  为`JdbcConfig`加上注释`@Configuration`

  ```java
  package org.config;
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  
  import javax.sql.DataSource;
  
  @Configuration
  public class JdbcConfig {
      // 定义一个方法获取要管理的对象
      @Bean
      public DataSource dataSource() {
          // 这里不能写接口
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName("com.mysql.jdbc.Driver");
          ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
          ds.setUsername("root");
          ds.setPassword("root");
          return ds;
      }
  }
  ```

  在SpringConfig类上，加上注释`@ComponentScan`扫描`JdbcConfig`所在的包

  ```java
  package org.config;
  
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  
  import javax.sql.DataSource;
  
  @Configuration
  @ComponentScan("org.config")
  public class SpringConfig {
  }
  ```

- 导入式

  直接在SpringConfig类加入注释`@Import(class)`，参数表示另外的配置类路径

  如果有多个的话，同样使用数组形式
  
  ##### 建议使用这种方式导入其他的配置类
  
  ```java
  package org.config;
  
  
  import com.alibaba.druid.pool.DruidDataSource;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.context.annotation.Import;
  
  import javax.sql.DataSource;
  
  @Configuration
  //@ComponentScan("org.config")
  @Import(JdbcConfig.class)
public class SpringConfig {
      
  }
  ```
  
  

