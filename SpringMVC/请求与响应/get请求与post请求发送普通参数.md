### get请求与post请求发送普通参数

-------

##### get请求传参

- **普通参数**：url地址传参，地址参数名与形参变量名相同，定义形参即可接收参数
  
- 后端
  
    ```java
    package com.itheima.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.ResponseBody;
    
    @Controller
    public class UserController {
    
        // 普通参数
        @RequestMapping("/commonParam")
        @ResponseBody
        public String commonParam(String name, int age) {
            System.out.println("普通参数传递 name ==> " + name);
            System.out.println("普通参数传递 age ==> " + age);
            return "{'module' : 'common param'}";
        }
    }
  ```
  
- 使用postman模拟浏览器发送请求
  
    img
  
- get请求一般没有在链接上输入汉字的需求

##### post请求

- **普通参数**：form表单post请求传参，表单参数名与形参变量名相同，定义形参即可接收参数
  
- 后端
  
    ```java
    package com.itheima.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.ResponseBody;
    
    @Controller
    public class UserController {
    
        // 普通参数
        @RequestMapping("/commonParam")
        @ResponseBody
        public String commonParam(String name, int age) {
            System.out.println("普通参数传递 name ==> " + name);
            System.out.println("普通参数传递 age ==> " + age);
            return "{'module' : 'common param'}";
        }
    }
  ```
  
- 使用postman模拟浏览器发送请求
  
    img
  
- **中文处理**

  post请求体的参数中有中文

  - 后端接收到中文参数会发生乱码

  - 在服务器启动配置类`ServletContainersInitConfig`进行乱码处理

    个人猜测：相当于在web容器加过滤器

    ```java
    import org.springframework.web.filter.CharacterEncodingFilter;
    
    //乱码处理，配字符编码过滤器
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8"); // 设置字符集
        return new Filter[]{filter};
    }
    ```

---------------

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
}
```