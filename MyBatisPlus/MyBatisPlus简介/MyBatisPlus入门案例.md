### MyBatisPlus入门案例

--------------------

- MyBatisPlus（简称MP）是基于MyBatis框架基础上开发的增强型工具，旨在简化开发、提高效率

##### 开发方式

- 基于MyBatis使用MyBatisPlus
- 基于Spring使用MyBatisPlus
- **基于SpringBoot使用MyBatisPlus**

##### SpringBoot整合MyBatis开发过程

- 创建SpringBoot工程
- 勾选配置使用的技术集（MyBatis、Mysql）
- 设置dataSource相关属性（JDBC参数）
- 定义数据层接口映射配置

--------------

##### SpringBoot整合MyBatisPlus入门程序

1. 创建新模块，选择Spring初始化，并配置模块相关基础信息

2. 选择当前模块需要使用的技术集（仅保留JDBC）

3. 手动添加MyBatisPlus起步依赖

   ```xml
   <dependency>
       <groupId>com.baomidou</groupId>
       <artifactId>mybatis-plus-boot-starter</artifactId>
       <version>3.4.1</version>
   </dependency>
   ```

   由于MyBatisPlus并未被收录到idea的系统内置配置，无法直接选择加入

4. 设置JDBC参数（application.yml）

   ```yaml
   server:
     port: 80
   
   spring:
     datasource:
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://localhost:3306/mybatisplus_db
       username: root
       password: zxy19820DL
       type: com.alibaba.druid.pool.DruidDataSource
   ```

5. 制作实体类与表结构（类名与表名对应，属性名与字段名对应）

   ```java
   package com.example.domain;
   
   public class User {
   
       private Long id;
       private String name;
       private String password;
       private Integer age;
       private String tel;
       
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", password='" + password + '\'' +
                   ", age=" + age +
                   ", tel='" + tel + '\'' +
                   '}';
       }
   
       public Long getId() {
           return id;
       }
   
       public void setId(Long id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   
       public Integer getAge() {
           return age;
       }
   
       public void setAge(Integer age) {
           this.age = age;
       }
   
       public String getTel() {
           return tel;
       }
   
       public void setTel(String tel) {
           this.tel = tel;
       }
   
   }
   ```

6. **定义数据接口，继承BaseMapper<T>，T为实体类**

   ```java
   package com.example.dao;
   
   import com.baomidou.mybatisplus.core.mapper.BaseMapper;
   import com.example.domain.User;
   import org.apache.ibatis.annotations.Mapper;
   
   @Mapper
   public interface UserDao extends BaseMapper<User> {
   }
   ```

7. 测试类中注入dao接口，测试功能

   ```java
   package com.example;
   
   import com.example.dao.UserDao;
   import com.example.domain.User;
   import org.junit.jupiter.api.Test;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.test.context.SpringBootTest;
   
   import java.util.List;
   
   @SpringBootTest
   class MybatisPlus01QuickstartApplicationTests {
   
       @Autowired
       private UserDao userDao;
   
       @Test
       void testGetAll() {
           List<User> users = userDao.selectList(null);
           System.out.println(users);
       }
   
   }
   ```