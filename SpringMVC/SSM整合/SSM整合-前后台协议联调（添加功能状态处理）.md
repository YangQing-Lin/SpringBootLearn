### SSM整合-前后台协议联调（添加功能状态处理）

--------------

##### 操作状态

- 成功

- 失败

  修改dao层的代码，得到行计数（当前操作影响几行）

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
      int save(Book book);
  
      @Update("update tbl_book set type = #{type}, name = #{name}, description = #{description} where id = #{id}")
      int update(Book book);
  
      @Delete("delete from tbl_book where id = #{id}")
      int delete(Integer id);
  
      @Select("select * from tbl_book where id = #{id}")
      Book getById(Integer id);
  
      @Select("select * from tbl_book")
      List<Book> getAll();
  
  }
  ```

  修改业务层代码，通过dao层的方法返回值（行计数）判断是否操作成功

  ```java
  package org.service.impl;
  
  import org.controller.Code;
  import org.dao.BookDao;
  import org.domain.Book;
  import org.exception.BusinessException;
  import org.exception.SystemException;
  import org.service.BookService;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Service;
  
  import java.util.List;
  
  @Service
  public class BookServiceImpl implements BookService {
  
      @Autowired
      private BookDao bookDao;
  
      public boolean save(Book book) {
          return bookDao.save(book) > 0; // 通过dao层方法返回的行计数判断操作是否成功
      }
  
      public boolean update(Book book) {
          return bookDao.update(book) > 0;
      }
  
      public boolean delete(Integer id) {
          return bookDao.delete(id) > 0;
      }
  
      public Book getById(Integer id) {
          if (id == 1) {
              throw new BusinessException(Code.BUSINESS_ERR, "请根据规范输入！");
          }
  
          // 将可能出现的异常进行包装，转换成自定义异常
          try {
              int i = 1/0;
          } catch (Exception e) {
              throw new SystemException(Code.SYSTEM_TIMEOUT_ERR, "服务器访问超时，请重试！", e);
          }
          return bookDao.getById(id);
      }
  
      public List<Book> getAll() {
          return bookDao.getAll();
      }
  }
  ```

- 服务器问题

##### 最后无论执行成功失败，都要获取更新之后的数据

```java
//添加
handleAdd () {
    // 发送ajax请求
    axios.post("/books", this.formData).then((res)=>{
        console.log(res.data);

        // 如果操作成功，关闭弹窗，显示更新之后的数据
        if (res.data.code === 20011) {
            this.dialogFormVisible = false;
            this.$message.success("添加成功");
        } else if (res.data.code === 20010) {
            this.$message.error("添加失败");
        } else {
            this.$message.error(res.data.msg);
        }
    }).finally(()=>{
        this.getAll();
    });
},
```

##### 弹窗时要将之前的数据清掉

```java
//弹出添加窗口
handleCreate() {
    this.dialogFormVisible = true;
    this.resetForm();
},

//重置表单
resetForm() {
    this.formData = {};
},
```