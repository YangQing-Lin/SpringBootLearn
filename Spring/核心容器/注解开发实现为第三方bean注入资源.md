### 注解开发实现为第三方bean注入资源

---------------

##### 简单类型

如果第三方bean缺的是简单类型

使用成员变量，加载第三方bean需要的属性

在第三方bean里，定义简单类型变量，并使用注释`@Value()`为其赋值

然后在返回bean的方法里调用这些变量

```java
package org.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

//@Configuration
public class JdbcConfig {

    @Value("com.mysql.jdbc.Driver")
    private String driver;
    @Value("jdbc:mysql://localhost:3306/spring_db")
    private String url;
    @Value("root")
    private String username;
    @Value("root")
    private String password;


    // 定义一个方法获取要管理的对象
    @Bean
    public DataSource dataSource() {
        // 这里不能写接口
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(username);
        ds.setPassword(password);
        return ds;
    }
}
```

这里也可以不直接赋值，使用加载properties文件的方式获取键值

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/spring_db
username=root
password=root
```

```java
package org.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@PropertySource("jdbc.properties")
//@Configuration
public class JdbcConfig {


    // 定义一个方法获取要管理的对象
    @Bean
    public DataSource dataSource() {
        // 这里不能写接口
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("${driver}");
        ds.setUrl("${url}");
        ds.setUsername("${username}");
        ds.setPassword("${password}");
        return ds;
    }
}
```

------------------

##### 引用类型

如果第三方bean缺的是引用类型资源

**引用类型注入只需要为bean定义方法设置形参即可，容器会根据类型自动装配对象**

这里假设BookDao为需要的引用类型资源

需要将其配置成bean

```java
package org.dao.impl;

import org.dao.BookDao;
import org.springframework.stereotype.Repository;

@Repository
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ... success");
    }
}
```

将需要的引用类型资源作为返回bean的方法形参

```java
package org.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.dao.BookDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@PropertySource("jdbc.properties")
//@Configuration
public class JdbcConfig {


    // 定义一个方法获取要管理的对象
    @Bean
    public DataSource dataSource(BookDao bookDao) {
        System.out.println(bookDao);

        // 这里不能写接口
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("${driver}");
        ds.setUrl("${url}");
        ds.setUsername("${username}");
        ds.setPassword("${password}");
        return ds;
    }
}
```