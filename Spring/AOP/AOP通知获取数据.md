### AOP通知获取数据

------

##### 三种数据

数据来源都是原始操作

- 获取切入点方法的参数

  所有的通知类型都能拿到原始操作的参数

  - `JoinPoint`：适用于前置、后置、返回后、抛出异常后通知
  - `ProceedJoinPoint`：适用于环绕通知

- 获取切入点方法返回值

  必须保证原始操作正常执行、正常结束

  只有以下两种通知才可以拿到返回值

  - 返回后通知
  - 环绕通知

- 获取切入点方法运行异常信息

  只有以下两种通知才可以拿到异常信息

  - 抛出异常后通知
  - 环绕通知

-----------------

##### 如何获取数据

- 参数的传递

	- 前置通知

    通过通知方法的形参`JoinPoint jp`

    `Object[] args = jp.getArgs()`获取原始方法的参数的对象数组

	  ```java
  //JoinPoint：用于描述切入点的对象，必须配置成通知方法中的第一个参数，可用于获取原始方法调用的参数
	  @Before("pt()")
  public void before(JoinPoint jp) {
        Object[] args = jp.getArgs();
      System.out.println(Arrays.toString(args));
        System.out.println("before advice ..." );
  }
    ```
  
  - 后置通知，同前置通知
  
    ```java
    //    @After("pt()")
    public void after(JoinPoint jp) {
        Object[] args = jp.getArgs();
        System.out.println(Arrays.toString(args));
        System.out.println("after advice ...");
    }
    ```
  
  - **环绕通知**
  
  	获取原始方法的参数的方式同前置通知，因为`JoinPoint`是`ProceedingJoinPoint`的父接口
  
  	**注意**，可以利用这个特性增加代码健壮性
  
  	环绕通知调用原始方法的时候可以向他传递获取到的原始操作的参数，并且我们也可以修改，这样我们就可以检查参数，如果有问题，我们可以先处理一下，然后传递给`pjp.proceed(args)`运行
  	
  - 返回后通知和抛出异常后通知获取参数的方法同前置通知
  
- 返回值的获取

  - 环绕通知，直接定义一个`Object`类型的变量接收方法调用的返回值即可

  - 返回后通知

    - 先定义一个能够接收返回值的形参

    - 修改注释为`@AfterReturning(value = "pt()", returning = "形参名")`

      ```java
      //设置返回后通知获取原始方法的返回值，要求returning属性值必须与方法形参名相同
      //    @AfterReturning(value = "pt()",returning = "ret")
      public void afterReturning(Object ret) {
          System.out.println("afterReturning advice ..."+ret);
      }
      ```

      **注意**

      如果该通知还有形参：`JoinPoint jp`，则必须放在第一个

      ```java
      //设置返回后通知获取原始方法的返回值，要求returning属性值必须与方法形参名相同
      //    @AfterReturning(value = "pt()",returning = "ret")
      public void afterReturning(JoinPoint jp,String ret) {
          System.out.println("afterReturning advice ..."+ret);
      }
      ```
  
- 异常信息的获取

  - 环绕通知，使用`try catch`捕获异常

  - 抛出异常后通知

    在通知方法里定义一个形参接收异常信息

    并在注解里标注：`@AfterThrowing(value = "pt()",throwing = "形参名")`

    ```java
    //设置抛出异常后通知获取原始方法运行时抛出的异常对象，要求throwing属性值必须与方法形参名相同
    //    @AfterThrowing(value = "pt()",throwing = "t")
    public void afterThrowing(Throwable t) {
        System.out.println("afterThrowing advice ..."+t);
    }
    ```

    

  