### Spring整合JUnit

---------------

##### 整合方式

- 导入JUnit坐标，导入Spring整合JUnit坐标

- 创建测试类`AccountServiceTest`，测试业务层接口

  - **设置类运行器**，Spring整合JUnit专用类运行器（`SpringJUnit4ClassRunner`）

    ```java
    //设置类运行器
    @RunWith(SpringJUnit4ClassRunner.class)
    ```

  - **设置Spring环境对应的配置类**

    ```java
    //设置Spring环境对应的配置类
    @ContextConfiguration(classes = SpringConfig.class)
    ```

  - 定义业务层接口，自动装配

    ```java
    //支持自动装配注入bean
    @Autowired
    private AccountService accountService;
    ```

  - 编写测试方法，需要加注释`@Test`

    想测哪个方法就测哪个方法

    ```java
    @Test
    public void testFindById(){
        System.out.println(accountService.findById(1));
    
    }
    ```

  直接运行即可查看测试结果

  -----------

  ##### 总结

  Spring整合JUnit就记三个固定格式

  - 导入坐标

  - 设置类运行器

    ```java
    //设置类运行器
    @RunWith(SpringJUnit4ClassRunner.class)
    ```

  - 设置Spring环境对应的配置类

    ```java
    //设置Spring环境对应的配置类
    @ContextConfiguration(classes = SpringConfig.class)
    ```

  然后想测哪个类，就将哪个类对应的bean通过自动装配的方式注入进来

  最后编写测试方法运行即可