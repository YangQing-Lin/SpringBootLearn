### AOP工作流程

---------

##### 工作流程

1. Spring容器启动

2. 读取所有切面配置中的切入点

   只读取用到的切入点

   在下面的例子中，ptx方法表示的切入点并没有被读取

   ```java
   package com.itheima.aop;
   
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   import org.springframework.stereotype.Component;
   
   @Component
   @Aspect
   public class MyAdvice {
       
       @Pointcut("execution(void com.itheima.dao.BookDao.save())")
       private void ptx() {};
   
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

3. 初始化bean，判定bean对应的类中的方法是否匹配到任意切入点

   - 匹配失败，创建对象（正常初始化bean）

   - 匹配成功，创建原始对象（**目标对象**）的**代理**对象

     Spring容器里此时保存的就是代理对象，而不是目标对象

   Spring的aop内部就是用代理模式

4. 获取bean执行方法

   - 获取bean，调用方法并执行，完成操作
   - 获取的bean是代理对象时，根据代理对象的运行模式运行原始方法与增强的内容，完成操作

##### 注意

我们在获取到一个可以和切入点匹配的bean时

- 如果直接打印对象，得到的还是bean的实现类，因为对`toString()`进行了重写
- 但是如果使用方法`getClas()`，打印出来是一个代理类

-------------

##### AOP核心概念

- 目标对象（Target）：原始功能去掉共性功能对应的类产生的对象，这种对象是无法直接完成最终工作的
- 代理（Proxy）：目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现
- SpringAOP本质：代理模式