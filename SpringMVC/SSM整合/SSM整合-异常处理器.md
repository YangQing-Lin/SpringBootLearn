### SSM整合-异常处理器

--------------------

##### 问题

- 程序开发过程中不可避免的会遇到异常现象

##### 出现异常现象的常见位置与常见诱因如下

- 框架内部抛出异常：因使用不合规导致
- 数据层抛出的异常：因外部服务器故障导致（例如：服务器访问超时）
- 业务层抛出的异常：因业务逻辑书写错误导致（例如：遍历业务书写操作，导致索引异常等）
- 表现层抛出的异常：因数据收集、校验等规则导致（例如：不匹配的数据类型间导致异常）
- 工具类抛出的异常：因工具类书写不严谨不够健壮导致（例如：必要释放的连续长期未释放等）

##### 思考

1. 各个层级均出现异常，异常处理代码书写在哪一层？

   将所有的异常往上抛出，集中在一处处理

   **所有的异常均抛出到表现层进行处理**

2. 表现层处理异常，每个方法中单独书写（`try catch`），代码书写量巨大且意义不强，如何解决?

   **AOP思想**

##### 分析

1. 异常要分类处理，不同的异常处理方式不同
2. 异常要集中放到表现层处理
3. 异常要使用AOP的思想处理

##### SpringMVC为我们提供了异常处理器

异常处理器

- 集中的、统一的处理项目中出现的异常

参考格式

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandle(Exception.class)
    public Result doException(Exception ex) {
        return new Result(666, null);
    }
}
```

--------------------

##### 实现

由于集中到表现层处理异常，所以在controller包下实现异常处理器`ProjectExceptionAdvice`

- 声明该类是做异常处理的，加注解`@RestControllerAdvice`

- 在该类中编写方法处理异常

  通过形参，将异常的对象传到处理器中

- 为该方法增加异常处理标志，表示其处理什么异常，加注解`@ExceptionHandler(Exception.class)`，参数表示拦截的异常的种类

- 拦截到异常后，需要给前端反馈，参照数据传输协议（Result）

**注意：**保证SpringMVC加载到了该配置类（`@ComponentScan("org.controller")`）

```java
package org.controller;

import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class ProjectExceptionAdvice {

    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex) {
        System.out.println("处理 Exception 异常 success");
        return new Result(666, null, "处理 Exception 异常 success");
    }
}
```

---------------

##### 注解

- `RestControllerAdvice`

  - AOP思想

  - 类注解
  - Rest风格开发的控制器增强类定义上方
  - 为Rest风格开发的控制器类做增强
  - 此注解自带`ResponseBody`注解与`@Component`注解，具备对应的功能

- `ExceptionHandle`

  - 方法注解
  - 专用于异常处理的控制器方法上方
  - 设置指定异常（通过参数指定）的处理方案，功能等同于控制器方法，出现异常后终止原始控制器执行，并转入当前方法执行
  - 此类方法可以根据处理的异常不同，制作多个方法分别处理对应的异常

---------------

##### 效果

正常的访问得到Result数据，异常的访问也会得到Result数据，专业！