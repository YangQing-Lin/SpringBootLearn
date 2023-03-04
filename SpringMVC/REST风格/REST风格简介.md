### REST风格简介

----------------

##### REST简介

- REST（Representational  State  Transfer），表现形式状态转换

  - 传统风格资源描述形式
    - http://localhost/user/getById?id=1
    - http://localhost/user/saveUser
  - REST风格描述形式（与上述形式一一对应）
    - http://localhost/user/1
    - http://localhost/user

- 优点

  - 书写简化
  - 隐藏资源的访问行为，无法通过地址得知对资源是何种操作

- 按照REST风格访问资源时使用的**行为动作**区分对资源进行了何种操作

  - http://localhost/users					    查询全部用户信息	GET（查询）
  - http://localhost/users/1                    查询指定用户信息     GET（查询）
  - http://localhost/users                        添加用户信息            POST（新增/保存）
  - http://localhost/users                        修改用户信息            PUT（修改/更新）
  - http://localhost/users/1                     删除指定用户信息    DELETE（删除）

  **通过url路径和请求的方式就可以确定一个资源的访问行为**

- 根据REST风格对资源进行访问称为**RESTful**

##### 注意事项

- 上述行为是约定方式，约定不是规范，是可以打破的，所以称REST风格，而不是REST规范

- 描述模块的名称通常使用复数，也就是加 $s$ 的格式描述，表示此类资源，而非单个资源，例如：users，books，accounts......