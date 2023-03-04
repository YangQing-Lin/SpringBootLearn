### SpringBoot整合JUnit

-------------

##### 步骤

- 编写测试类
  - 在测试类上加注解`@SpringBootTest`
  - 注入要测试的资源
  - 直接编写测试方法测试该资源的功能

-------------

##### 注解

- `SpringBootTest`

  - 类型：测试类注解

  - 位置：测试类定义上方

  - 作用：设置JUnit加载的SpringBoot启动类

  - 范例：

    ```java
    @SpringBoot(classes = Springboot07JunitApplication.class)
    class Springboot07JunitApplicationTests {}
    ```

  - 相关属性

    - classes：设置SpringBoot启动类

  - 注意事项

    如果测试类在SpringBoot启动类的包或子包中，可以省略启动类的设置，也就是省略classes的设定