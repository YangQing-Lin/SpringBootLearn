### SpringBoot整合MyBatis

--------------

##### 基于SpringBoot实现SSM整合

- SpringBoot整合Spring（不存在）
- SpringBoot整合SpringMVC（不存在）
- SpringBoot整合MyBatis（主要）

-------------

##### 步骤

1. 创建新项目，选择Spring初始化，并配置模块相关基础信息

2. 选择当前模块需要使用的技术集（MyBatis、Mysql）

3. 设置数据源

   type是数据源对象

   ```yml
   spring:
     datasource:
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://localhost:3306/ssm_db
       username: root
       password: zxy19820DL
       type: com.alibaba.druid.pool.DruidDataSource
   ```

   **注意事项：**

   SpringBoot版本低于2.4.3（不含），Mysql驱动版本大于8.0时，需要在url连接串中配置时区

   `url: jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC`

   或在Mysql数据库端配置时区解决此问题

4. 定义数据层接口与映射配置

   ```java
   package com.example.dao;
   
   import com.example.domain.Book;
   import org.apache.ibatis.annotations.Mapper;
   import org.apache.ibatis.annotations.Select;
   
   @Mapper
   public interface BookDao {
       @Select("select * from tbl_book where id = #{id}")
       Book getById(Integer id);
   }
   ```

5. 测试类中注入dao接口，测试功能

   ```java
   package com.example;
   
   import com.example.dao.BookDao;
   import com.example.domain.Book;
   import org.junit.jupiter.api.Test;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.test.context.SpringBootTest;
   
   @SpringBootTest
   class SpringBoot08MybatisApplicationTests {
   
       @Autowired
       private BookDao bookDao;
   
       @Test
       void testGetById() {
           Book book = bookDao.getById(2);
           System.out.println(book);
       }
   
   }
   ```

