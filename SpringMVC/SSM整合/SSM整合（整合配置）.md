### SSM整合（整合配置）

-----------------

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

------------------

##### 创建工程

- 使用模板：maxven...webapp

- 导入坐标

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
  
    <groupId>org.example</groupId>
    <artifactId>ssm_demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
  
    <dependencies>
      
      <!--spring整合springmvc-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.10.RELEASE</version>
      </dependency>
  
      <!--jdbc-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.10.RELEASE</version>
      </dependency>
  
      <!--spring整合Junit-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.2.10.RELEASE</version>
      </dependency>
  
      <!--mybatis-->
      <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.6</version>
      </dependency>
  
      <!--spring整合mybatis-->
      <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.0</version>
      </dependency>
  
      <!--mysql驱动-->
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
      </dependency>
  
      <!--阿里巴巴数据源-->
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.16</version>
      </dependency>
  
      <!--JUnit-->
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
      </dependency>
  
      <!--web3.0容器-->
      <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
      </dependency>
  
      <!--json数据转换-->
      <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.0</version>
      </dependency>
    </dependencies>
  
    <build>
      <plugins>
        <!--tomcat服务器-->
        <plugin>
          <groupId>org.apache.tomcat.maven</groupId>
          <artifactId>tomcat7-maven-plugin</artifactId>
          <version>2.1</version>
          <configuration>
            <port>80</port>
            <path>/</path>
          </configuration>
        </plugin>
      </plugins>
    </build>
  </project>
  ```

----------------------

##### 整合配置

- 文件层次（/org/）

  - config

  - domain

    对应实体类

  - dao

    没有impl包，因为开发mybatis的时候，使用自动代理创建实现类

  - service

    - impl

  - dao

- 配置文件

  - /org/config/SpringConfig.java

    **这里一定要写`classpath`，很关键！血的教训！**
  
    ```java
    package org.config;
    
    import org.springframework.context.annotation.*;
    import org.springframework.context.support.PropertySourcesPlaceholderConfigurer;
    import org.springframework.test.annotation.ProfileValueSourceConfiguration;
    
    @Configuration
    @ComponentScan({"org.service"})
    @PropertySource("classpath:jdbc.properties")
    @Import({JdbcConfig.class, MyBatisConfig.class})
    public class SpringConfig {
    }
  
    ```

  - 编写jdbc.properties文件
  
    ```properties
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/ssm_db?useSSL=false
    jdbc.username=root
  jdbc.password=zxy19820DL
    ```

  - /org/config/JdbcConfig.java
  
    ```java
    package org.config;
    
    import com.alibaba.druid.pool.DruidDataSource;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Bean;
    
    import javax.sql.DataSource;
    
    public class JdbcConfig {
    
        @Value("${jdbc.driver}")
        private String driver;
        @Value("${jdbc.url}")
        private String url;
        @Value("${jdbc.username}")
        private String username;
        @Value("${jdbc.password}")
        private String password;
    
        @Bean
        public DataSource dataSource() {
            DruidDataSource dataSource = new DruidDataSource();
            dataSource.setDriverClassName(driver);
            dataSource.setUrl(url);
            dataSource.setUsername(username);
            dataSource.setPassword(password);
            return dataSource;
        }
  }
    ```

  - /org/config/MyBatisConfig.java
  
    ```java
    package org.config;
    
    import org.mybatis.spring.SqlSessionFactoryBean;
    import org.mybatis.spring.mapper.MapperScannerConfigurer;
    import org.springframework.context.annotation.Bean;
    
    import javax.sql.DataSource;
    
    public class MyBatisConfig {
    
        @Bean
        public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource) {
            SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
            factoryBean.setDataSource(dataSource);
            factoryBean.setTypeAliasesPackage("org.domain");
            return factoryBean;
        }
    
        @Bean
        public MapperScannerConfigurer mapperScannerConfigurer() {
            MapperScannerConfigurer msc = new MapperScannerConfigurer();
            msc.setBasePackage("org.dao");
            return msc;
        }
  }
    ```

  - SpringMVC的两个配置类

    - ServletConfig
  
      - 父子容器
      
        SpringMvc的容器可以访问Spring的容器
      
        Spring的容器不可以访问SpringMvc的容器
      
      ```java
      package org.config;
      
      import org.springframework.web.filter.CharacterEncodingFilter;
      import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;
      
      import javax.servlet.Filter;
      
      public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
          protected Class<?>[] getRootConfigClasses() {
              return new Class[]{SpringConfig.class};
          }
      
          protected Class<?>[] getServletConfigClasses() {
              return new Class[]{SpringMvcConfig.class};
          }
      
          protected String[] getServletMappings() {
              return new String[]{"/"};
          }
      
          // 乱码处理
          @Override
          protected Filter[] getServletFilters() {
              CharacterEncodingFilter filter = new CharacterEncodingFilter();
              filter.setEncoding("UTF-8");
              return new Filter[]{filter};
          }
      }
      ```
      
    - SpringMvcConfig
    
      ```java
      package org.config;
      
      import org.springframework.context.annotation.ComponentScan;
      import org.springframework.context.annotation.Configuration;
      import org.springframework.web.servlet.config.annotation.EnableWebMvc;
      
      @Configuration
      @ComponentScan("org.controller")
      @EnableWebMvc
      public class SpringMvcConfig {
      }
      ```

