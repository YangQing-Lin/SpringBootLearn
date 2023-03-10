### AOP简介

--------------

##### 简介

- AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，指导开发者如何组织程序结构

  - OOP(Object Oriented Programming)面向对象编程

- 作用：在**不惊动原始设计**的基础上为其进行**功能增强**

  源代码不需要改，功能却可以改变
  
- Spring理念：无入侵式/无侵入式编程

  源代码里没有Spring的痕迹，但是功能却加上了

  例如之前的setter注入，并没有改变源代码，但是属性的对象加上了

--------------

##### 核心概念

- 首先我们先找到原始程序中需要共性的功能抽出来，做成一个通知类的通知方法

  方法里就是共性功能

- 原始程序中的方法叫做连接点，也就是说这些方法未来是可以被连接的（变成切入点）

- 通过切面，找到需要执行通知的原始程序里的方法，将这些方法定义成切入点

  连接点是所有方法，切入点是某些会执行通知的方法

  切面就是通知与切入点之间的关系，其描述的就是在哪些切入点上执行哪些通知

#####   官方解释概念

- **连接点**（JoinPoint）：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量等
  
  - 在SpringAOP中，理解为方法的执行
  
- **切入点**（PointCut）：**匹配连接点的式子**
  - 在SpringAOP中，**一个切入点可以只描述一个具体方法，也可以匹配多个方法**
    - 一个具体方法：org.dao包下的BookDao接口中的无形参无返回值的save()方法
    - 匹配多个方法：所有的save方法，所有的get开头的方法，所有以Dao结尾的接口中的任意方法，所有带有一个参数的方法
  
  切入点一定在连接点中
  
  要追加功能的方法
  
- **通知**（Advice）：在切入点处执行的操作，也就是共性功能
  
  - 在SpringAOP中，功能最终以方法的形式呈现
  
- **通知类**：定义通知的类

- **切面**（Aspect）：描述通知与切入点的对应关系

  比如通知A绑定了哪些切入点，这个绑定关系就是切面