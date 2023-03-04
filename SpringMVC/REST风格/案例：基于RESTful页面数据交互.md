### 案例：基于RESTful页面数据交互

-------------------------

##### 后端接口开发

开发中一般不管 $id$，因为数据库一般都会设为自增

注意加注解`@RequestBody`获取请求体里的json数据

```java
package com.itheima.controller;

import com.itheima.domain.Book;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
@RequestMapping("/books")
public class BookController {

    @PostMapping
    public String save(@RequestBody Book book) {
        System.out.println("book save ==> " + book);
        return "{'module' : 'book save success'}";
    }

    @GetMapping
    public List<Book> getAll() {
        Book book1 = new Book();
        book1.setType("算法");
        book1.setName("算法导论");
        book1.setDescription("算法导论666");

        Book book2 = new Book();
        book2.setType("算法");
        book2.setName("算法进阶指南");
        book2.setDescription("算法进阶指南666");

        List<Book> bookList = new ArrayList<Book>();
        bookList.add(book1); bookList.add(book2);
        return bookList;
    }
}
```

---------------

##### 页面访问处理

**放行非SpringMVC的请求**

将页面包放到webapp目录下，通过链接`localhost/pages/books.html`访问页面

**SpringMVC应该对该链接放行（不拦截）**，换做tomcat对该链接处理并展示页面

创建一个工具类，专门让SpringMVC放行静态资源（css，js等）的访问

```java
package com.itheima.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    //设置静态资源访问过滤，当前类需要设置为配置类，并被扫描加载
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        //当访问/pages/????时候，从/pages目录下查找内容
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```

设置SpringMVC**启动的时候加载这一组配置**

在该配置类上加注解`@Configuration`

在SpringMVC配置类上扫描到该配置类所在的包

**Ajax请求**

查询操作

```html
                //主页列表查询
                getAll() {
                    axios.get("/books").then((res)=>{
                        this.dataList = res.data;
                    });
                },
```

添加操作

```html
                //添加
                saveBook () {
                    axios.post("/books",this.formData).then((res)=>{

                    });
                },
```

-------------

##### 总结

- 先做后台功能，开发接口并调通接口
- 再做页面异步调用，确认功能可以正常访问
- 最后完成页面数据展示
- 补充：放行静态资源访问