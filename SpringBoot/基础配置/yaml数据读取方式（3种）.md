### yaml数据读取方式（3种）

---------

##### yaml数据读取

```yaml
lesson: SpringBoot

server:
  port: 80

student:
  name: ddl
  age: 18
  tel: 18066073126
  subject:
    - java
    - 算法
    - vue
```

- 使用`@Value`读取单个数据，属性名引用方式：`${一级属性名.二级属性名}`

  ```java
  package com.example.controller;
  
  import com.example.domain.Student;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.core.env.Environment;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  import javax.xml.ws.soap.Addressing;
  
  
  @RestController
  @RequestMapping("/books")
  public class BookController {
      
      @Value("${lesson}")
      private String lesson;
      @Value("${server.port}")
      private Integer port;
      @Value("${student.subject[0]}")
      private String subject_00;
  
  }
  ```

- 封装全部数据到`Environment`对象

  ```java
  package com.example.controller;
  
  import com.example.domain.Student;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.core.env.Environment;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  import javax.xml.ws.soap.Addressing;
  
  
  @RestController
  @RequestMapping("/books")
  public class BookController {
  
      @Autowired
      private Environment environment;
      
      @GetMapping("/{id}")
      public String getById(@PathVariable Integer id) {
          System.out.println(environment.getProperty("lesson"));
          System.out.println(environment.getProperty("server.port"));
          System.out.println(environment.getProperty("student.name"));
          System.out.println(environment.getProperty("student.subject[2]"));
      }
  
  }
  ```

- 自定义对象封装指定数据

  ```java
  package com.example.domain;
  
  import org.springframework.boot.context.properties.ConfigurationProperties;
  import org.springframework.stereotype.Component;
  
  import java.util.Arrays;
  
  @Component
  @ConfigurationProperties(prefix = "student")
  public class Student {
      private String name;
      private Integer age;
      private String tel;
      private String[] subject;
  
      @Override
      public String toString() {
          return "Student{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  ", tel='" + tel + '\'' +
                  ", subject=" + Arrays.toString(subject) +
                  '}';
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public Integer getAge() {
          return age;
      }
  
      public void setAge(Integer age) {
          this.age = age;
      }
  
      public String getTel() {
          return tel;
      }
  
      public void setTel(String tel) {
          this.tel = tel;
      }
  
      public String[] getSubject() {
          return subject;
      }
  
      public void setSubject(String[] subject) {
          this.subject = subject;
      }
  }
  ```

  使用的时候利用自动装配即可获取属性数据

  **注意：自定义对象封装数据警告解决方案**

  加依赖

  ```xml
  <!--自定义对象封装数据警告解决方案-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```


----------------

```java
package com.example.controller;

import com.example.domain.Student;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.env.Environment;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/books")
public class BookController {

    // 第一种读数据的方式，使用属性占位符
    @Value("${lesson}")
    private String lesson;
    @Value("${server.port}")
    private Integer port;
    @Value("${student.subject[0]}")
    private String subject_00;

    // 第二种读数据的方式，使用 Environment 加载所有的环境信息
    @Autowired
    private Environment environment;

    // 第三种读数据的方式
    @Autowired
    private Student student;


    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id) {
        System.out.println(lesson);
        System.out.println(port);
        System.out.println(subject_00);
        System.out.println("----------------------------");
        System.out.println(environment.getProperty("lesson"));
        System.out.println(environment.getProperty("server.port"));
        System.out.println(environment.getProperty("student.name"));
        System.out.println(environment.getProperty("student.subject[2]"));
        System.out.println("----------------------------");
        System.out.println(student);
        return "Hello，SpringBoot！";
    }
}
```