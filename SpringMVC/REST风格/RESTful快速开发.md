### RESTful快速开发

------------

快速开发，就要将繁琐重复的操作或者代码简化

##### 路径有重复的前缀

- 路径前缀放到模块处理器类上

  ```java
  package com.itheima.controller;
  
  import com.itheima.domain.Book;
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.*;
  
  @Controller
  @RequestMapping("/books")
  public class BookController {
  
      @RequestMapping(method = RequestMethod.POST)
      @ResponseBody
      public String save(@RequestBody Book book) {
          System.out.println("book save ... " + book);
          return "{'module' : 'book save'}";
      }
  
      @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
      @ResponseBody
      public String delete(@PathVariable Integer id) {
          System.out.println("book delete ... " + id);
          return "{'module' : 'book delete'}";
      }
  
      @RequestMapping(method = RequestMethod.PUT)
      @ResponseBody
      public String update(@RequestBody Book book) {
          System.out.println("book update ... " + book);
          return "{'module': 'book update'}";
      }
  
      @RequestMapping(value = "/{id}", method = RequestMethod.GET)
      @ResponseBody
      public String getById(@PathVariable Integer id) {
          System.out.println("book getById ... " + id);
          return "{'module' : 'book getById'}";
      }
  
      @RequestMapping(method = RequestMethod.GET)
      @ResponseBody
      public String getAll() {
          System.out.println("book getAll ... ");
          return "{'module' : 'book getAll'}";
      }
  }
  ```

##### 每一个处理器方法都要写`@RequestBody`

将处理器方法的`@RequestBody`删去，在模块处理器类上加上该注释

而后发现，每个模块的处理器类都要写注释`@Controller`和`@ResponseBody`，依然很繁琐

所以将两个注释合二为一，推出了`@RestController`

```java
package com.itheima.controller;

import com.itheima.domain.Book;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

//@Controller
//@ResponseBody
@RestController
@RequestMapping("/books")
public class BookController {

    @RequestMapping(method = RequestMethod.POST)
    public String save(@RequestBody Book book) {
        System.out.println("book save ... " + book);
        return "{'module' : 'book save'}";
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
    public String delete(@PathVariable Integer id) {
        System.out.println("book delete ... " + id);
        return "{'module' : 'book delete'}";
    }

    @RequestMapping(method = RequestMethod.PUT)
    public String update(@RequestBody Book book) {
        System.out.println("book update ... " + book);
        return "{'module': 'book update'}";
    }

    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public String getById(@PathVariable Integer id) {
        System.out.println("book getById ... " + id);
        return "{'module' : 'book getById'}";
    }

    @RequestMapping(method = RequestMethod.GET)
    public String getAll() {
        System.out.println("book getAll ... ");
        return "{'module' : 'book getAll'}";
    }
}
```

##### 每个处理器方法都要用`@RequestMapping`的参数`method`指定请求动作

换成更加简介的注解`@PostMapping`、`@DeleteMapping`、`@PutMapping`、`@GetMapping`，一一对应

```java
package com.itheima.controller;

import com.itheima.domain.Book;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

//@Controller
//@ResponseBody
@RestController
@RequestMapping("/books")
public class BookController {

//    @RequestMapping(method = RequestMethod.POST)
    @PostMapping
    public String save(@RequestBody Book book) {
        System.out.println("book save ... " + book);
        return "{'module' : 'book save'}";
    }

//    @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
    @DeleteMapping("/{id}")
    public String delete(@PathVariable Integer id) {
        System.out.println("book delete ... " + id);
        return "{'module' : 'book delete'}";
    }

//    @RequestMapping(method = RequestMethod.PUT)
    @PutMapping
    public String update(@RequestBody Book book) {
        System.out.println("book update ... " + book);
        return "{'module': 'book update'}";
    }

//    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id) {
        System.out.println("book getById ... " + id);
        return "{'module' : 'book getById'}";
    }

//    @RequestMapping(method = RequestMethod.GET)
    @GetMapping
    public String getAll() {
        System.out.println("book getAll ... ");
        return "{'module' : 'book getAll'}";
    }
}
```

---------------

##### 注解

- `@RestController`
  - 类注解
  - 基于SpringMVC的RESTful开发控制器类定义上方
  - 设置当前控制器类为RESTful风格，等同于`@Controller`和`@ResponseBody`两个注解组合功能
- `@PostMapping`、`@DeleteMapping`、`@PutMapping`、`@GetMapping`
  - 方法注解
  - 基于SpringMVC的RESTful开发控制器方法定义上方
  - 设置当前控制器方法请求访问路径与请求动作，每种对应一个请求动作，例如`@GetMapping`对应Get请求
  - 属性：value，请求访问路径























```java
package com.itheima.controller;

import com.itheima.domain.Book;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

//@Controller
//@ResponseBody配置在类上可以简化配置，表示设置当前每个方法的返回值都作为响应体
//@ResponseBody
@RestController     //使用@RestController注解替换@Controller与@ResponseBody注解，简化书写
@RequestMapping("/books")
public class BookController {

//    @RequestMapping( method = RequestMethod.POST)
    @PostMapping        //使用@PostMapping简化Post请求方法对应的映射配置
    public String save(@RequestBody Book book){
        System.out.println("book save..." + book);
        return "{'module':'book save'}";
    }

//    @RequestMapping(value = "/{id}" ,method = RequestMethod.DELETE)
    @DeleteMapping("/{id}")     //使用@DeleteMapping简化DELETE请求方法对应的映射配置
    public String delete(@PathVariable Integer id){
        System.out.println("book delete..." + id);
        return "{'module':'book delete'}";
    }

//    @RequestMapping(method = RequestMethod.PUT)
    @PutMapping         //使用@PutMapping简化Put请求方法对应的映射配置
    public String update(@RequestBody Book book){
        System.out.println("book update..."+book);
        return "{'module':'book update'}";
    }

//    @RequestMapping(value = "/{id}" ,method = RequestMethod.GET)
    @GetMapping("/{id}")    //使用@GetMapping简化GET请求方法对应的映射配置
    public String getById(@PathVariable Integer id){
        System.out.println("book getById..."+id);
        return "{'module':'book getById'}";
    }

//    @RequestMapping(method = RequestMethod.GET)
    @GetMapping             //使用@GetMapping简化GET请求方法对应的映射配置
    public String getAll(){
        System.out.println("book getAll...");
        return "{'module':'book getAll'}";
    }
}
```