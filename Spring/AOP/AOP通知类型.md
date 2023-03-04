### AOP通知类型

----------

AOP通知描述了抽取的共性功能，根据共性功能抽取的位置不同，最终运行代码时要将其加入到合理的位置

##### 根据不同的位置，可以分为五种通知类型

- 前置通知，在原始方法运行之前执行

  ```java
  //@Before：前置通知，在原始方法运行之前执行
  //    @Before("pt()")
  public void before() {
      System.out.println("before advice ...");
  }
  ```

  也可以写成`@Before("MyAdvice.pt()")`，不过切入点和通知在一个类里，所以没必要

- 后置通知

  ```java
  //@After：后置通知，在原始方法运行之后执行
  //    @After("pt2()")
  public void after() {
      System.out.println("after advice ...");
  }
  ```

- **环绕通知**

  可以模拟出来任意通知类型

  ```java
  //@Around：环绕通知，在原始方法运行的前后执行
  //    @Around("pt()")
  public Object around(ProceedingJoinPoint pjp) throws Throwable {
      System.out.println("around before advice ...");
      //表示对原始操作的调用
      Object ret = pjp.proceed();
      System.out.println("around after advice ...");
      return ret;
  }
  ```

  需要有表达式表示对原始操作的调用，不然不知道具体在原始操作前执行还是后执行

  **为什么要抛出异常？**

  因为原始操作的调用无法预期有没有异常

  **如果原始操作有返回值呢？**

  需要将通知的返回值置为`Object`，在调用原始操作时将返回值存下来，然后在增强功能之后，返回这个返回值（通知可以直接篡改返回值）

- 返回后通知

  ```java
  //@AfterReturning：返回后通知，在原始方法执行完毕后运行，且原始方法执行过程中未出现异常现象
  //    @AfterReturning("pt2()")
  public void afterReturning() {
      System.out.println("afterReturning advice ...");
  }
  ```

  只有当原始方法没有抛异常，正常结束的时候，该通知才会生效

  注意与后置通知区分

- 抛出异常后通知

  ```java
  //@AfterThrowing：抛出异常后通知，在原始方法执行过程中出现异常后运行
  @AfterThrowing("pt2()")
  public void afterThrowing() {
      System.out.println("afterThrowing advice ...");
  }
  ```

----------------

##### `@Around`注意事项

- 环绕通知必须依赖形参`ProceedingJoinPoint`才能实现对原始方法的调用，进而实现原始方法调用前后同时添加通知

- 通知中如果未使用`ProceedingJoinPoint`对原始方法进行调用将跳过原始方法执行

  利用这个特性，可以做隔离（权限校验）

- 对原始方法的调用可以不接收返回值，通知方法设置成`void`即可，如果接收返回值，必须设定为`Object`类型

- 原始方法的返回值如果是`void`类型，通知方法的返回值类型可以设置成`void`，也可以设置成`Object`

- 由于无法预知原始方法运行后是否会抛出异常，因此环绕通知方法必须抛出`Throwable`对象

环绕通知标准格式

```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("around before advice ...");
    Object ret = pjp.proceed();
    System.out.println("around after advice ...");
    return ret;
}
```

