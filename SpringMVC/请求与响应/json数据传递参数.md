### json数据传递参数

-------------------

##### 准备

需要jackson包做数据的类型转换（json数据 ==> 特定类型）

导入坐标

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```

##### postman如何发送json数据

img

##### 后端接收到json数据后，如何转换为特定类型

在SpringMVC的配置类（SpringMvcConfig）加上注解`@EnableWebMvc`开启SpringMvc对json数据转换成对象的功能

**`@EnableWebMvc`**注解功能强大，该注解整合了多个功能，此处仅使用其中一部分功能，即json数据进行自动转换

```java
package com.itheima.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

import javax.servlet.Filter;
import javax.servlet.annotation.WebFilter;

@Configuration
@ComponentScan("com.itheima.controller")
//开启json数据类型自动转换
@EnableWebMvc
public class SpringMvcConfig {
}
```

------------------

##### 集合参数[简单类型]：json

- 后端

  由于请求的参数是在请求体里，所以加上注解告知`@RequestBody`

  **`@RequestBody`**将请求中请求体所包含的数据传递给请求参数，**此注解一个处理器方法只能使用一次**

  ```java
  // 集合参数：json格式
  @RequestMapping("/listParamForJson")
  @ResponseBody
  public String listParamForJson(@RequestBody List<String> likes) {
      System.out.println("list common(json)参数传递 list ==> " + likes);
      return "{'module' : 'list common for json param'}";
  }
  ```

##### POJO参数：json

- 后端

  ```java
  // pojo参数：json格式
  @RequestMapping("/pojoParamForJson")
  @ResponseBody
  public String pojoParamForJson(@RequestBody User user) {
      System.out.println("pojo(json)参数传递 user ==> " + user);
      return "{'module' : 'pojo for json param'}";
  }
  ```

- 使用postman发送请求

  键值对

  img

##### 集合参数[pojo数据]：json格式

- 后端

  ```java
  // 集合参数[pojo数据]：json格式
  @RequestMapping("/listPojoParamForJson")
  @ResponseBody
  public String listPojoParamForJson(@RequestBody List<User> list) {
      System.out.println("list pojo(json)参数传递 list ==> " + list);
      return "{'module' : 'list pojo for json param'}";
  }
  ```

- 使用postman发送请求

  img

-----------------

##### `@RequestBody`与`@RequestParam`的区别

- 区别
  - `@RequestParam`用于接收url地址传参，表单传参【application/x-www-form-urlencoded】
  - `@RequestBody`用于接收json数据【application/json】
- 应用
  - 后期开发中，发送json格式数据为主，`@RequestBody`应用较广
  - 如果发送非json格式数据，选用`@RequestParam`接收请求参数

-------------

```java
package com.itheima.controller;

import com.itheima.domain.User;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.Arrays;
import java.util.Date;
import java.util.List;
//请求参数
@Controller
public class UserController {

    //普通参数：请求参数与形参名称对应即可完成参数传递
    @RequestMapping("/commonParam")
    @ResponseBody
    public String commonParam(String name ,int age){
        System.out.println("普通参数传递 name ==> "+name);
        System.out.println("普通参数传递 age ==> "+age);
        return "{'module':'common param'}";
    }

    //普通参数：请求参数名与形参名不同时，使用@RequestParam注解关联请求参数名称与形参名称之间的关系
    @RequestMapping("/commonParamDifferentName")
    @ResponseBody
    public String commonParamDifferentName(@RequestParam("name") String userName , int age){
        System.out.println("普通参数传递 userName ==> "+userName);
        System.out.println("普通参数传递 age ==> "+age);
        return "{'module':'common param different name'}";
    }

    //POJO参数：请求参数与形参对象中的属性对应即可完成参数传递
    @RequestMapping("/pojoParam")
    @ResponseBody
    public String pojoParam(User user){
        System.out.println("pojo参数传递 user ==> "+user);
        return "{'module':'pojo param'}";
    }

    //嵌套POJO参数：嵌套属性按照层次结构设定名称即可完成参数传递
    @RequestMapping("/pojoContainPojoParam")
    @ResponseBody
    public String pojoContainPojoParam(User user){
        System.out.println("pojo嵌套pojo参数传递 user ==> "+user);
        return "{'module':'pojo contain pojo param'}";
    }

    //数组参数：同名请求参数可以直接映射到对应名称的形参数组对象中
    @RequestMapping("/arrayParam")
    @ResponseBody
    public String arrayParam(String[] likes){
        System.out.println("数组参数传递 likes ==> "+ Arrays.toString(likes));
        return "{'module':'array param'}";
    }

    //集合参数：同名请求参数可以使用@RequestParam注解映射到对应名称的集合对象中作为数据
    @RequestMapping("/listParam")
    @ResponseBody
    public String listParam(@RequestParam List<String> likes){
        System.out.println("集合参数传递 likes ==> "+ likes);
        return "{'module':'list param'}";
    }


    //集合参数：json格式
    //1.开启json数据格式的自动转换，在配置类中开启@EnableWebMvc
    //2.使用@RequestBody注解将外部传递的json数组数据映射到形参的集合对象中作为数据
    @RequestMapping("/listParamForJson")
    @ResponseBody
    public String listParamForJson(@RequestBody List<String> likes){
        System.out.println("list common(json)参数传递 list ==> "+likes);
        return "{'module':'list common for json param'}";
    }

    //POJO参数：json格式
    //1.开启json数据格式的自动转换，在配置类中开启@EnableWebMvc
    //2.使用@RequestBody注解将外部传递的json数据映射到形参的实体类对象中，要求属性名称一一对应
    @RequestMapping("/pojoParamForJson")
    @ResponseBody
    public String pojoParamForJson(@RequestBody User user){
        System.out.println("pojo(json)参数传递 user ==> "+user);
        return "{'module':'pojo for json param'}";
    }

    //集合参数：json格式
    //1.开启json数据格式的自动转换，在配置类中开启@EnableWebMvc
    //2.使用@RequestBody注解将外部传递的json数组数据映射到形参的保存实体类对象的集合对象中，要求属性名称一一对应
    @RequestMapping("/listPojoParamForJson")
    @ResponseBody
    public String listPojoParamForJson(@RequestBody List<User> list){
        System.out.println("list pojo(json)参数传递 list ==> "+list);
        return "{'module':'list pojo for json param'}";
    }

}
```

