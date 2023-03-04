### AOP入门案例

--------------------

案例设定：测定接口执行效率

简化设定：在接口执行前输出当前系统的时间

开发模式：XML or **注解**

##### 思路分析

1. 导入坐标（pom.xml）
2. 制作连接点方法（原始操作，Dao接口与实现类）
3. 制作共性功能（通知类和通知）
4. 定义切入点，后续将功能追加给切入点
5. 绑定切入点与通知关系（切面）

--------------

##### 实现

1. 导入AOP相关坐标

   导入`spring-context`之后，`spring-aop`是自动导入的，这两个包有依赖关系

   导入`aspect`包

   ```xml
     <dependencies>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>5.2.10.RELEASE</version>
       </dependency>
         
       <dependency>
         <groupId>org.aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>1.9.4</version>
       </dependency>
     </dependencies>
   ```

2. 制作连接点方法，定义dao接口和实现类

3. 抽出共性功能做成通知类里的通知

   ```java
   package com.itheima.aop;
   
   public class MyAdvice {
       // 通知
       public void method() {
           System.out.println(System.currentTimeMillis());
       }
   }
   ```

4. 定义切入点

   **说明：切入点定义依托于一个不具有实际意义的私有方法进行，即无参数，无返回值，方法体无实际逻辑**

   ```java
   package com.itheima.aop;
   
   import org.aspectj.lang.annotation.Pointcut;
   
   public class MyAdvice {
   
       // 定义切入点
       @Pointcut("execution(void com.itheima.dao.BookDao.update())")
       private void pt() {};
   
       // 通知
       public void method() {
           System.out.println(System.currentTimeMillis());
       }
   }
   ```

5. 绑定切入点与通知的关系，并**指定通知添加到原始连接点的具体执行位置**

   ```java
   package com.itheima.aop;
   
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   
   public class MyAdvice {
   
       // 定义切入点
       @Pointcut("execution(void com.itheima.dao.BookDao.update())")
       private void pt() {};
       
       // 切面，描述该通知与指定切入点的关系，before表示在方法执行前
       @Before("pt()")
       // 通知
       public void method() {
           System.out.println(System.currentTimeMillis());
       }
   }
   ```

6. 定义通知类受Spring容器管理，**并定义当前类为切面类**，加上注解`@Aspect`

   将通知类变成受Spring控制的bean，加上注解`@Component`

   ```java
   package com.itheima.aop;
   
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   import org.springframework.stereotype.Component;
   
   @Component
   public class MyAdvice {
   
       // 定义切入点
       @Pointcut("execution(void com.itheima.dao.BookDao.update())")
       private void pt() {};
   
       // 切面，描述该通知与指定切入点的关系，before表示在方法执行前
       @Before("pt()")
       // 通知
       public void method() {
           System.out.println(System.currentTimeMillis());
       }
   }
   ```

   告诉Spring，在扫描到该bean的时候，将其作为AOP编程处理，加上注解`@Aspect`

   ```java
   package com.itheima.aop;
   
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   import org.springframework.stereotype.Component;
   
   @Component
   @Aspect
   public class MyAdvice {
   
       // 定义切入点
       @Pointcut("execution(void com.itheima.dao.BookDao.update())")
       private void pt() {};
   
       // 切面，描述该通知与指定切入点的关系，before表示在方法执行前
       @Before("pt()")
       // 通知
       public void method() {
           System.out.println(System.currentTimeMillis());
       }
   }
   ```

7. 开启Spring对AOP注解驱动支持

   告诉Spring有用注解开发的AOP编程，在Spring配置类上加上注解`@EnableAspectJAutoProxy`

   即`@EnableAspectJAutoProxy`启动了`@Aspect`，`@Aspect`又告诉Spring应该作为AOP编程处理，而不是普通的bean

   ```java
   package com.itheima.config;
   
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.EnableAspectJAutoProxy;
   
   @Configuration
   @ComponentScan("com.itheima")
   //开启注解开发AOP功能
   @EnableAspectJAutoProxy
   public class SpringConfig {
   }
   ```

   