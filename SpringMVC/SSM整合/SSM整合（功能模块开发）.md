### SSM整合（功能模块开发）

---------------------

##### SSM整合流程

1. 创建工程
2. ssm整合
   - Spring
     - SpringConfig
   - MyBatis
     - MybatisConfig
     - JdbcConfig
     - jdbc.properties
   - SpringMVC
     - ServletConfig
     - SpringMvcConfig
3. 功能模块
   - 表与实体类
   - dao（接口+自动代理）
   - service（接口+实现类）
     - 业务层接口测试（整合JUnit）
   - controller
     - 表现层接口测试（PostMan）

---------------------------

##### 功能模块开发

- 表

  img

- 实体类（/org/domain/Book.java）

  ```java
  package org.domain;
  
  public class Book {
      private Integer id;
      private String  type;
      private String  name;
      private String  description;
  
      @Override
      public String toString() {
          return "Book{" +
                  "id=" + id +
                  ", type='" + type + '\'' +
                  ", name='" + name + '\'' +
                  ", description='" + description + '\'' +
                  '}';
      }
  
      public Integer getId() {
          return id;
      }
  
      public void setId(Integer id) {
          this.id = id;
      }
  
      public String getType() {
          return type;
      }
  
      public void setType(String type) {
          this.type = type;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getDescription() {
          return description;
      }
  
      public void setDescription(String description) {
          this.description = description;
      }
  }
  ```

- 数据层

  - 接口（/org/dao/BookDao.java）

    该接口提供增删改查功能

    ```java
    package org.dao;
    
    import org.domain.Book;
    
    import java.util.List;
    
    public interface BookDao {
    
        // 增删改查
        void save(Book book);
    
        void update(Book book);
    
        void delete(Integer id);
    
        Book getById(Integer id);
    
        List<Book> getAll();
    
    }
    ```

  - 实现接口

    **使用MyBatis的自动代理创建实现**

    ```java
    package org.dao;
    
    import org.apache.ibatis.annotations.Delete;
    import org.apache.ibatis.annotations.Insert;
    import org.apache.ibatis.annotations.Select;
    import org.apache.ibatis.annotations.Update;
    import org.domain.Book;
    
    import java.util.List;
    
    public interface BookDao {
    
        // 增删改查
        @Insert("insert into tbl_book values(null, #{type}, #{name}, #{description})")
        void save(Book book);
    
        @Update("update tbl_book set type = #{type}, name = #{name}, description = #{description} where id = #{id}")
        void update(Book book);
    
        @Delete("delete from tbl_book where id = #{id}")
        void delete(Integer id);
    
        @Select("select * from tbl_book where id = #{id}")
        Book getById(Integer id);
    
        @Select("select * from tbl_book")
        List<Book> getAll();
    
    }
    ```

- 业务层

  - 接口（/org/service/BookService.java）

    写明文档

    ```java
    package org.service;
    
    import org.apache.ibatis.annotations.Delete;
    import org.apache.ibatis.annotations.Insert;
    import org.apache.ibatis.annotations.Select;
    import org.apache.ibatis.annotations.Update;
    import org.domain.Book;
    
    import java.util.List;
    
    public interface BookService {
    
        /**
         * 保存
         * @param book
         * @return
         */
        boolean save(Book book);
    
        /**
         * 修改
         * @param book
         * @return
         */
        boolean update(Book book);
    
        /**
         * 根据id删除
         * @param id
         * @return
         */
        boolean delete(Integer id);
    
        /**
         * 根据id查询
         * @param id
         * @return
         */
        Book getById(Integer id);
    
        /**
         * 查询全部
         * @return
         */
        List<Book> getAll();
    }
    ```

  - 实现接口

    使用自动装配dao接口

    **注意**：在使用idea做ssm整合的时候，如果要注入的对象在整个系统中不存在（Spring没有配相关bean，这里用的是自动代理），idea会检查并报错。但这不是真错了，所以我们可以配置idea在自动装配的时候不需要对bean的类型做检测，这样就可以避免报错了

    ```java
    package org.service.impl;
    
    import org.dao.BookDao;
    import org.domain.Book;
    import org.service.BookService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import java.util.List;
    
    @Service
    public class BookServiceImpl implements BookService {
    
        @Autowired
        private BookDao bookDao;
    
        public boolean save(Book book) {
            bookDao.save(book);
            return true;
        }
    
        public boolean update(Book book) {
            bookDao.update(book);
            return true;
        }
    
        public boolean delete(Integer id) {
            bookDao.delete(id);
            return true;
        }
    
        public Book getById(Integer id) {
            return bookDao.getById(id);
        }
    
        public List<Book> getAll() {
            return bookDao.getAll();
        }
    }
    ```

  - 控制层（表现层）

    - 需要调业务层接口

    ```java
    package org.controller;
    
    import org.domain.Book;
    import org.service.BookService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.*;
    
    import java.util.List;
    
    @RestController
    @RequestMapping("/books")
    public class BookController {
    
        @Autowired
        private BookService bookService;
    
        @PostMapping
        public boolean save(@RequestBody Book book) {
            return bookService.save(book);
        }
    
        @PutMapping
        public boolean update(@RequestBody Book book) {
            return bookService.update(book);
        }
    
        @DeleteMapping("/{id}")
        public boolean delete(@PathVariable Integer id) {
            return bookService.delete(id);
        }
    
        @GetMapping("/{id}")
        public Book getById(@PathVariable Integer id) {
            return bookService.getById(id);
        }
    
        @GetMapping
        public List<Book> getAll() {
            return bookService.getAll();
        }
    }
    ```

    