### RESTful入门案例

-----------------

路径上的资源名应该加上s，表示一类资源

##### 如何设置请求行为

设定http的请求动作

注解`@RequestMapping`有参数`method`指定请求方法是哪种

```java
@RequestMapping(value = "/users", method = RequestMethod.POST)
@ResponseBody
public String save() {
    System.out.println("user save ... ");
    return "{'module' : 'user save'}";
}
```

##### 参数的传递

设定请求参数（路径变量）

REST风格的参数在请求路径上，处理器方法想要获取路径上的参数需要加注解`@PathVariable`

**该注解的作用**：绑定路径参数与处理器方法形参间的关系，要求路径参数名与形参名一一对应

```java
@RequestMapping(value = "/users", method = RequestMethod.DELETE)
@ResponseBody
public String delete(@PathVariable Integer id){
    System.out.println("user delete... " + id);
    return "{'module':'user delete'}";
}
```

映射的路径需要加路径参数，`@RequestMapping(value = "/users/{id}", method = RequestMethod.DELETE)`

```java
@RequestMapping(value = "/users/{id}", method = RequestMethod.DELETE)
@ResponseBody
public String delete(@PathVariable Integer id){
    System.out.println("user delete... " + id);
    return "{'module':'user delete'}";
}
```

----------------

##### 注解：`@RequestBody`、`@RequestParam`、`@PathVariable`

- 区别
  - `@RequestParam`用于接收url地址传参或表单传参
  - `@RequestBody`用于接收json数据
  - `@PathVariable`用于接收路径参数，使用 {参数名称} 描述路径参数
- 应用
  - 后期开发中，发送请求参数超过1个小时，以json格式为主，`@RequestBody`应用较广
  - 如果发送非json格式数据，选用`@RequestParam`接收请求参数
  - 采用RESTful进行开发，当参数数量较少时，例如1个，可以采用`@PathVariable`接收请求路径变量，通常用于传递id值













































































```java
package com.itheima.controller;

import com.itheima.domain.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
public class UserController {

    //设置当前请求方法为POST，表示REST风格中的添加操作
    @RequestMapping(value = "/users",method = RequestMethod.POST)
    @ResponseBody
    public String save(){
        System.out.println("user save...");
        return "{'module':'user save'}";
    }

    //设置当前请求方法为DELETE，表示REST风格中的删除操作
    //@PathVariable注解用于设置路径变量（路径参数），要求路径上设置对应的占位符，并且占位符名称与方法形参名称相同
    @RequestMapping(value = "/users/{id}",method = RequestMethod.DELETE)
    @ResponseBody
    public String delete(@PathVariable Integer id){
        System.out.println("user delete..." + id);
        return "{'module':'user delete'}";
    }

    //设置当前请求方法为PUT，表示REST风格中的修改操作
    @RequestMapping(value = "/users",method = RequestMethod.PUT)
    @ResponseBody
    public String update(@RequestBody User user){
        System.out.println("user update..."+user);
        return "{'module':'user update'}";
    }

    //设置当前请求方法为GET，表示REST风格中的查询操作
    //@PathVariable注解用于设置路径变量（路径参数），要求路径上设置对应的占位符，并且占位符名称与方法形参名称相同
    @RequestMapping(value = "/users/{id}" ,method = RequestMethod.GET)
    @ResponseBody
    public String getById(@PathVariable Integer id){
        System.out.println("user getById..."+id);
        return "{'module':'user getById'}";
    }

    //设置当前请求方法为GET，表示REST风格中的查询操作
    @RequestMapping(value = "/users",method = RequestMethod.GET)
    @ResponseBody
    public String getAll(){
        System.out.println("user getAll...");
        return "{'module':'user getAll'}";
    }

}









/*
    @RequestMapping
    @ResponseBody
    public String delete(){
        System.out.println("user delete...");
        return "{'module':'user delete'}";
    }

    @RequestMapping
    @ResponseBody
    public String update(){
        System.out.println("user update...");
        return "{'module':'user update'}";
    }

    @RequestMapping
    @ResponseBody
    public String getById(){
        System.out.println("user getById...");
        return "{'module':'user getById'}";
    }

    @RequestMapping
    @ResponseBody
    public String getAll(){
        System.out.println("user getAll...");
        return "{'module':'user getAll'}";
    }
    */
```