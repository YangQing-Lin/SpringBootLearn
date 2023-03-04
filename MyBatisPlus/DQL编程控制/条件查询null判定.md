### 条件查询null判定

---------------

##### null值处理

- if语句控制条件追加

  ```java
  // null判定
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.lt(User::getAge, uq.getAge2());
  if (null != uq.getAge()) {
      lqw.gt(User::getAge, uq.getAge());
  }
  List<User> users = userDao.selectList(lqw);
  System.out.println(users);
  ```

- 条件参数控制

  ```java
  // null判定
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  // 先判定第一个参数是否为true，如果为true连接当前条件，否则不连接当前条件
  // 同样支持链式编程
  lqw.lt(null != uq.getAge2(), User::getAge, uq.getAge2());
  lqw.gt(null != uq.getAge(), User::getAge, uq.getAge());
  List<User> users = userDao.selectList(lqw);
  System.out.println(users);
  ```

- 条件参数控制（链式编程）

  ```java
  // null判定
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  // 先判定第一个参数是否为true，如果为true连接当前条件，否则不连接当前条件
  // 同样支持链式编程
  lqw.lt(null != uq.getAge2(), User::getAge, uq.getAge2())
      .gt(null != uq.getAge(), User::getAge, uq.getAge());
  List<User> users = userDao.selectList(lqw);
  System.out.println(users);
  ```