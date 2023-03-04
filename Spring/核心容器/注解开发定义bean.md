### 注解开发

----------

从Spring2.0开始，逐渐提供了一系列的注解，到Spring2.5的时候，注解就比较完善了

##### 接下来学习如何使用Spring2.5提供的一系列注解来简化开发

到了Spring3.0，推出了纯注解开发

-------------

##### 具体实现

```xml
<bean id="bookDao" class="org.dao.impl.BookDaoImpl"/>
```

以上配置存在三个信息

1. 配置一个bean
2. 设置配置的bean的类型（实现类）
3. 起个名称

如何使用注解开发实现上述三个信息呢

在实现类上方加入注解：`@Component`，表示配置了一个bean，`@Component("name")`为配置的类起一个名称。

```java
package org.dao.impl;

import org.dao.BookDao;
import org.springframework.stereotype.Component;

@Component("bookDao")
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ... success");
    }
}
```

可以不指定名称，后续获取该bean的时候，使用类型获取

##### 那么Spring的配置文件如何知道这里配置了一个bean呢

在配置文件里，定义一个命名空间：context

使用该命名空间的标签`context:component-scan`，属性`base-package`表示类所在的包名

会扫描该包名下的所有类以及子包的类

```xml
<context:component-scan base-package="org.dao.impl"/>
```

##### 总结

- 使用`@Component`定义bean

  ```java
  @Component("bookDao")
  public class BookDaoImpl implements BookDao {
  }
  
  @Component
  public class BookServiceImpl implements BookService {
  }
  ```

- 核心配置文件中通过组件扫描加载bean

  ```java
  <context:component-scan base-package="org"/>
  ```

##### 衍生注解

Spring提供了`@Component`注解的三个衍生注解

- `@Controller`：用于表现层bean定义
- `@Service`：用于业务层bean定义
- `@Repository`：用于数据层bean定义

这三个衍生注解其实与`@Component`功能一样，只不过看起来好理解