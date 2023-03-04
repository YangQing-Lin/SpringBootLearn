### Spring整合MyBatis思路分析

-----------

##### MyBatis程序核心对象分析

- 初始化`SqlSessionFactory`

  ```java
  // 1. 创建SqlSessionFactoryBuilder对象
  SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
  // 2. 加载SqlMapConfig.xml配置文件
  InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml.bak");
  // 3. 创建SqlSessionFactory对象
  SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
  ```

  `SqlSessionFactory`对象

- 获取连接，获取实现

  ```java
  // 4. 获取SqlSession
  SqlSession sqlSession = sqlSessionFactory.openSession();
  // 5. 执行SqlSession对象执行查询，获取结果User
  AccountDao accountDao = sqlSession.getMapper(AccountDao.class);
  ```

  `SqlSession`对象和`accountDao`对象

- 获取数据层接口

  ```java
  Account ac = accountDao.findById(2);
  System.out.println(ac);
  ```

- 关闭连接

  ```java
  // 6. 释放资源
  sqlSession.close();
  ```

该程序中最核心的对象是`SqlSessionFactory`对象

---------------------------

##### 整合MyBatis

所有配置都是围绕`SqlSessionFactory`对象进行的

- 初始化属性数据

  ```xml
  <configuration>
      <properties resource="jdbc.properties"></properties>
  ```

- 初始化类型别名

  ```xml
      <typeAliases>
          <package name="com.itheima.domain"/>
      </typeAliases>
  ```

- 初始化dataSource

  ```xml
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

- 初始化映射配置

  ```xml
      <mappers>
          <package name="com.itheima.dao"></package>
      </mappers>
  </configuration>
  ```

则MyBatis应该管理`SqlSessionFactory`对象

