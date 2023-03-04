### Spring整合MyBatis

--------------

##### 导入坐标

- Spring操作数据库专用的包

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.10.RELEASE</version>
  </dependency>
  ```

- Spring整合Mybatis使用的jar包，隶属于MyBatis

  ```xml
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
  </dependency>
  ```

----------------------

##### 注解配置

准备：SpringConfig和JdbcConfig

编写MybatisConfig专门配置MyBatis

- 配置`SqlSessionFactory`类型的bean

  Mybatis提供了`SqlSessionFactoryBean`类，该类继承了`FactoryBean<>`，用于造`SqlSessionFactory`对象

  只需要注入`SqlSessionFactoryBean`的必要属性即可

  这些必要属性在MyBatis的专用配置包里`SqlMapConfig.xml.bak`

  注意：使用`@Bean`创建bean，直接在方法的参数里写需要的依赖对象

  ```java
  //定义bean，SqlSessionFactoryBean，用于产生SqlSessionFactory对象
  @Bean
  public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
      SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
      ssfb.setTypeAliasesPackage("com.itheima.domain");
      ssfb.setDataSource(dataSource); 
      return ssfb;
  }
  ```

  以上替换的是配置里的

  ```xml
  <typeAliases>
      <package name="com.itheima.domain"/>
  </typeAliases>
  
  // 以下是直接注入了引用类型DataSource
  <environments default="mysql">
      <environment id="mysql">
          <transactionManager type="JDBC"></transactionManager>
          <dataSource type="POOLED">
              <property name="driver" value="${jdbc.driver}"></property>
              <property name="url" value="${jdbc.url}"></property>
              <property name="username" value="${jdbc.username}"></property>
              <property name="password" value="${jdbc.password}"></property>
          </dataSource>
      </environment>
  </environments>
  ```

- 配置`MapperScannerConfigurer`类型的bean

  ```java
  //定义bean，返回MapperScannerConfigurer对象
  @Bean
  public MapperScannerConfigurer mapperScannerConfigurer(){
      MapperScannerConfigurer msc = new MapperScannerConfigurer();
      msc.setBasePackage("com.itheima.dao");
      return msc;
  }
  ```

  以上替换的是配置中

  将`SqlSessionFactory`对象给下面的配置，用于造出自动代理对象

  最终使用代理的方式，实现dao的接口

  ```xml
  <mappers>
      <package name="com.itheima.dao"></package>
  </mappers>
  ```

##### 全新的运行方式

将原来的配置文件失效

```java
import com.itheima.config.SpringConfig;
import com.itheima.domain.Account;
import com.itheima.service.AccountService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App2 {
    public static void main(String[] args) {
        // 得到容器
        ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);

        // 取service对象，其依赖的dao对象已经自动注入
        AccountService accountService = ctx.getBean(AccountService.class);

        Account ac = accountService.findById(1);
        System.out.println(ac);
    }
}
```

---------------

##### 总结工作

Spring整合MyBatis

将原来MyBatis做的配置文件换成了Spring整合

1. 导入坐标
2. Spring配置不变（需要导入MyBatis配置类），数据源配置不变，就加上了一个MyBatisConfig配置类
3. 该类定义了两个bean，格式固定，只有两个参数需要适应性改变
   - 扫描类型别名的包
   - 扫描映射的包