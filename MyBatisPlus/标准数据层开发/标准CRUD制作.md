### 标准CRUD制作

----------------

##### 标准数据层CRUD功能

| 功能       | 自定义接口                             | MP接口                                       |
| ---------- | -------------------------------------- | -------------------------------------------- |
| 新增       | boolean save(T t)                      | int insert(T t)                              |
| 删除       | boolean delete(int id)                 | int deleteById(Serializable id)              |
| 修改       | boolean update(T t)                    | int updateById(T t)                          |
| 根据id查询 | T getById(int id)                      | T selectById(Serializable id)                |
| 查询全部   | List<T> getAll()                       | List<T> selectList()                         |
| 分页查询   | PageInfo<T> getAll(int page, int size) | IPage<T> selectPage(IPage<T> page)           |
| 按条件查询 | List<T> getAll(Condition condition)    | IPage<T> selectPage(Wrapper<T> queryWrapper) |

------------

##### Lombok包

一个java类库，提供了一组注解，简化POJO实体类开发

- 导入jar包

  ```xml
  <!--lombok-->
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <scope>provided</scope> <!--不需要参与最终打包-->
  </dependency>
  ```

- 添加注解

  `@Data`相当于`@Setter`、`@Getter`、`@ToString`、`@EqualsAndHashCode`

  `AllArgsConstructor`所有有参构造方法

  `NoArgsConstructor`无参构造方法

  ```java
  package com.example.domain;
  
  import lombok.*;
  
  // lombok
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  public class User {
  
      private Long id;
      private String name;
      private String password;
      private Integer age;
      private String tel;
  
  }
  ```