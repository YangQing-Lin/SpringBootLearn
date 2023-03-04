### AOP切入点表达式

----------

切入点：要进行增强的方法

切入点表达式：要进行增强的方法的描述方式

##### 描述方式

- 按接口描述

  执行com.itheima.dao包下的BookDao接口中的无参数update方法

  ```java
  execution(void com.itheima.dao.BookDao.update())
  ```

- 按实现类描述

  执行com.itheima.dao.impl包下的BookDaoImpl类中的无参数update方法

  ```java
  execution(void com.itheima.dao.impl.BookDaoImpl.update())
  ```

-------------------------

##### 切入点表达式标准格式

动作关键字（访问修饰符	返回值	包名.类/接口名.方法名(参数) 异常名）

```java
execution(public User com.itheima.service.UserService.findById(int))
```

- 动作关键字：描述切入点的行为动作，例如`execution`表示执行到指定切入点
- 访问修饰符：`public, private`等，可以省略
- 返回值类型
- 包名
- 类/接口名
- 方法名
- 参数
- 异常名：方法定义中抛出指定异常，可以省略

--------------

##### AOP切入点表达式

使用通配符描述切入点，快速描述

- *：单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配符出现

  ```java
  execution(public * com.itheima.*.UserService.find*(*))
  ```

  匹配`com.itheima`包下的任意包中的`UserService`类或接口中所有`find`开头的带有一个参数的方法

- ..：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写

  ```java
  execution(public User com..UserService.findById(..))
  ```

  匹配`com`包下的任意包中的`UserService`类/接口中所有名称为`findById`的方法

- +：专用于匹配子类类型

  ```java
  execution(* *..*Service+.*(..))
  ```

  任意返回值，`*..`任意包下的，`*Service+`名称以`Service`为结尾的类/接口的子类，任意方法，任意参数

  总结下来就是任意业务层子接口

-------------

##### 切入点表达式示例

```java
package com.itheima.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class MyAdvice {
    //切入点表达式：
//    @Pointcut("execution(void com.itheima.dao.BookDao.update())")           // 接口
//    @Pointcut("execution(void com.itheima.dao.impl.BookDaoImpl.update())")  // 实现类
//    @Pointcut("execution(* com.itheima.dao.impl.BookDaoImpl.update(*))")    // 不能匹配，因为 * 必须匹配一个参数
//    @Pointcut("execution(void com.*.*.*.update())")                         // 接口
//    @Pointcut("execution(void com.*.*.*.*.update())")                       // 实现类
//    @Pointcut("execution(* *..*(..))")                                      // ..匹配任意多个，代表匹配工程中所有的东西
//    @Pointcut("execution(* *..*e(..))")                                     // 范围匹配，以字符 e 为结尾的方法
//    @Pointcut("execution(void com..*())")                                   // 正向识别不清楚时，可以反向识别，()前一定是方法
//    @Pointcut("execution(* com.itheima.*.*Service.find*(..))")              // 给所有的业务层查询方法增强功能（aop）
    //执行com.itheima包下的任意包下的名称以Service结尾的类或接口中的save方法，参数任意，返回值任意
    @Pointcut("execution(* com.itheima.*.*Service.save(..))")
    private void pt(){}

    @Before("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

---------------

##### 书写技巧

精准高效

- 所有代码按照标准规范开发，否则以下技巧全部失效
- 描述切入点**通常描述接口**，而不描述实现类，如果描述实现类就**紧耦合**了，Spring讲究的尽量降低耦合度
- 访问控制修饰符针对接口开发均采用`public`描述（**可省略访问控制修饰符**）
- **返回值类型**对于增删改类使用精准类型加速匹配（增删改类的方法一般都是返回`boolean`），对于查询类使用 * 通配快速描述
- **包名**书写**尽量不使用 .. 匹配**，效率过低，常用 * 做单个包描述匹配，或精准匹配
- **接口名**/类名书写名称与模块相关的**采用 * 匹配**，例如`UserService`书写成`*Service`，绑定业务层接口
- **方法名**书写以**动词**进行**精准匹配**，名词采用 * 匹配，例如`getById`书写成`getBy*`，`selectAll`书写成`selectAll`
- 参数规则较为复杂，根据业务方法灵活调整
- 通常**不使用异常**作为**匹配**规则