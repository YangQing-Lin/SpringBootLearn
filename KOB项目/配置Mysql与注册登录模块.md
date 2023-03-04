### SpringBoot 配置Mysql与注册登录模块

2022/7/21

------------------------

#### 应用框架结构

​	SpringBoot 就是一个一直在跑的程序，与 client 端通信，等待 client 端发送请求，然后处理请求，返回数据

​	Mysql 同样是个独立的在跑的程序，等待接受请求，并处理请求，返回数据

​	SpringBoot 和 Mysql 之间是相互交互的关系

##### 	这里以 client 发送登录请求为例

当 client 端向 SpringBoot 发送登录请求（同时传有 username，password 等信息），SpringBoot 在接收到这个请求之后，会向 Mysql 发送请求（请求查询 username 下的所有信息），Mysql 会返回给 SpringBoot 这个 client 的各种信息（username 和 password 等信息），SpringBoot 在接收到 Mysql 返回的信息之后，会将 Mysql 返回的信息与 client 端发送的信息进行比对，以此来判断是否允许 client 端登录成功

Mysql 在交互的时候，会将数据存到硬盘上，部分数据也会存到内存里

----------------------

#### Mysql 的结构

- 包含多个数据库，db1, db2, kob ...

  每次操作的时候需要使用其中一个数据库

  - 每个数据库里有多个表
  - 每个表里存储多组数据

操作 Mysql 最简单的方式是命令行

-----------

SpringBoot 会根据用户的不同请求（注册，登录等），向 Mysql 发送不同的请求，Mysql 会根据不同的请求，执行相应的语句（insert, select 等），然后反馈给 SpringBoot ，SpringBoot 再根据结果，处理用户的请求

-----------------------------------------------------------------

#### 如何让 SpringBoot 操作 Mysql

##### 用`mybatis-plus`对接`Mysql`

[Maven仓库地址](https://mvnrepository.com/) 

[Mybatis-Plus官网](https://baomidou.com/)

##### 在 IDEA 里配置 SpringBoot，在 pom.xml 文件中添加依赖

在 Maven 仓库里找对应的配置加入：

- Spring Boot Starter JDBC
  - 提供了数据源配置、事务管理、数据访问等等功能
- Project Lombok
  - 简化代码
- MySQL Connector/J
  - 实现了JDBC，为使用java开发的程序提供连接，方便java程序操作数据库。
- mybatis-plus-boot-starter
  - 默认写好很多 sql 语句
- mybatis-plus-generator
  - 自动生成函数

SpringBoot 想要访问 Mysql 同样需要用户名密码来访问，即 root 用户以及 root 用户的 password，需要配置

```java
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/kob?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

--------------------------

#### SpringBoot 中层的概念（常用模块）

- pojo 层：将数据库中的表对应成 Java 中的 Class
- mapper 层（也叫 Dao 层）：将 pojo 层的 class 中的操作（crud），映射成 sql 语句
- service 层：写具体的业务逻辑，组合使用 mapper 中的操作，实现业务
- controller 层：负责请求转发，接受页面过来的参数，传给 Service 处理，接到返回值，再传给页面

----------------------------

#### 实现 pojo 层

在 backend 目录下创建软件包 pojo，在包里为每张表创建对应的类

pojo 里的一个对象对应数据库里的一行数据

```java
// 自动实现一些机械化方法
@Data // 填充常用函数
@NoArgsConstructor // 填充无参构造函数
@AllArgsConstructor // 填充所有参构造函数
```

------------------------------------

#### 实现 mapper 层

通过依赖：mybatis-plus，即可不用手写 sql 语句

直接调用 API ，即可实现 sql 语句

-----------------

#### 调试 service 和 controller 层

因为调试逻辑简单，所以可以放到一起调试

根据路由找到对应的函数，调用函数返回数据，同时可以取路由里的参数

-------------------------

以上，Mysql 就集成到项目中了

#### 应用框架结构

​	在 client 端操作的时候，先向 SpringBoot 发送一个请求（相当于是打开一个 url），**SpringBoot 会根据这个请求（url）找到应该调用的函数**，假设这个函数需要调用 Mysql，则 SpringBoot 就会向 Mysql 发送请求：查询结果，Mysql 处理完该请求后，就会返回给 SpringBoot ，然后 SpringBoot 再将结果返回给 client 端

-------------------------------

#### 用户认证操作

##### 授权机制

例如，某个用户想要查看某个 Bot 的代码，如果该用户不是该 Bot 的代码，那么该用户就没有权限查看

SpringBoot 里有权限判断模块，直接集成即可，Maven 官网搜 spring-boot-starter-security

默认 username 为 user，password 为随机生成的字符串

##### 一般网站默认认证方式都是通过 session 验证（传统登录认证方式），其原理为：

​	client 端将 username 和 password 等传到 SpringBoot 之后，SpringBoot 会在 Mysql 里查询，查询之后会将结果和 username，password 是否一致，如果一致就会将处理结果返回给 client 端

​	SpringBoot 在将结果返回给 client 端的时候，会在 session 里面传一个随机字符串（sessionID）给 client 端，在将 sessionID 传给 client 端之前，会将 sessionID 存下来（可以存到 Mysql 里，也可以存到 redis 里），client 端在拿到 sessionID 之后，就会将这个 sessionID 放到 cookie 里

​	未来每一次 client 在向 SpringBoot 发送请求时，都会自动默认从 cookie 里取出来 sessionID 放到 session 里面，并传给 SpringBoot，SpringBoot 会检查有没有 sessionID，如果有，就会从 Mysql（假设存到了Mysql里）里把 sessionID 读出来（Mysql 里存的是 sessionID 的各种信息），读出来之后会检查 sessionID 有没有过期

​	如果没有过期就表示当前 client 端的登录状态是成功的，即不需要判断权限了，如果过期了或者当前 sessionID 不存在，就表示当前 client 端的登录状态是过期的，同时会返回给 client 端登录页面

​	这里登录成功之后发送的 sessionID 类似于 “临时通行证”，client 端会在 cookie 里保存这张 “通行证”，之后client 端在向 SpringBoot 发送请求的时候都会带着这张 “通行证”，SpringBoot 会在 Mysql 里检查这张 “通行证” 有没有过期，是否合法，并且获取这张 “通行证” 对应的用户信息，当 “通行证” 没有过期的时候，就可以直接进行处理请求等操作，如果 “通行证” 过期了，就要重新办一张（重新登录获取 “通行证”）

##### kob项目采用 jwt 进行登陆验证

------------------------

#### Security 对接 Mysql

即通过 Mysql 来判断 client 端是否登录成功

##### 配置 Security 

在`Maven`仓库里搜索，添加依赖`spring-boot-starter-security`

##### 实现 service.impl.UserDetailsServiceImpl 类，继承自 UserDetailsService 接口，用来接入数据库信息

- 重写方法，Alt + insert, 选重写

- 该方法会读入一个 username，返回 uername 对应的信息

- 在 Mysql 里查询对应的信息，需要 sql 语句，通过 mapper 层可以直接实现

- 实现 UserDetails 接口

- 在 Mysql 里 password 前面加上 {noop}，表示密码明文存储，此为 SpringBoot 的作者习惯

- 如果不是明文存储，则需要写密码加密

  ##### 加密算法特性：

  给定给一个字符串 p1，可以很快的将 p1变成一个新的字符串 p2（加密）

  由 p1——> p2可以很快得出结果，由 p2——> p1求不出来（**加密过程不可逆**）

  如此，**Mysql 在存储用户密码的时候就不要存储明文，而是存储密文**，这样即使未来 Mysql 泄露，也不会泄露用户密码，更加安全

  ##### 用同一种加密方式，相同密码不同次加密后得到的 p2 是匹配的，这样就可以判断用户输入的密码是否正确

##### 实现 config.SecurityConfig 类，用来实现用户密码的加密存储

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

-------------------------------

2022/7/27

#### 采用`JWT`验证用户登录

将页面（`url`）分为两大类：

- 公开页面
  - `login` 页面
  - `registered` 页面
- 授权页面

##### 传统登录验证模式

​    用户登录的信息经过`SpringBoot`在`Mysql`的信息比对之后，发现一致，就会给用户发送一个`sessionID`，同时`SpringBoot`会存一份`sessionID`（可能在内存里可能在数据库里），用户收到之后会将其存到本地（浏览器`cookie`）里，未来用户在访问网站授权页面的时候，都会将在`cookie`里存的`sessionID`带上，`SpringBoot`在接收到用户访问授权页面的请求时，会将`sesionID`提取出来，判断`sesionID`是否有效

​    `SpringBoot`在存`sessionID`的时候，同时会存该`sessionID`对应着哪个用户，其实是存了一个映射，将`sessionID`映射到用户

​    当判断`sessionID`有效时，就会将这个`sessionID`对应的用户信息提取到上下文中，之后就可以正常访问`Controller` 

​	提取到上下文中 ，意思是，未来就可以在某个`Controller`中用过某些`API`来获取用户信息

​	如果判断`sessionID`时，发现不存在或者过期了，那么就不能访问授权页面

##### 为什么采用`JWT`而不是传统模式

​	现在有很多应用都是跨域的，而且很多应用不止有一个前端（web端，app端等），那么传统`session`模式就不是很方便，一旦跨域，`session`就难以处理了，并且不止一个服务器端的时候，传统`session`模式需要将`sessionID`复制到多台服务器上，比较麻烦

​	基于以上原因，采用现在比较流行的`JWT`验证方式，其优势在于：

1. 方便实现跨域
2. 不需要在服务器端存储，也就是说只要获取一个令牌，就可以用这个令牌登录多个服务器

##### `JWT`验证方式的原理

一个用户登录成功之后，服务器会给用户一个`JWT-token`，并且服务器不需要存储任何信息

`JWT-token`的组成：

用户的`userID`配合服务器端独有的密钥，根据某种固定的加密算法，得到一个加密之后的字符串

- 用户的`userID`
- 加密后的字符串

未来用户在向服务器端发送请求的时候，都会带上`JWT-token`

当用户想要访问某个授权页面时，会验证`JWT-token`，服务器在验证的时候，会将用户的`userID`加上服务器端独有的密钥进行加密，比对加密后的字符串与用户端的`JWT-token`里存的字符串是否一致

判断成功之后，会将用户的信息从数据库中提取到上下文中，然后再访问授权页面的`Controller` 

##### 因为信息是完全存在用户端的，如果用户篡改`JWT-token`里的`userID`，以访问原本不对该用户授权的页面怎么办？

这种情况不会发生，因为用户是不知道服务器端独有的密钥的，用户得到的是加密之后的结果，加密过程不可逆，

服务器端的密钥用户得不到，就不知道`JWT-token`里对应的加密结果如何更新，也就达不到越权的目的

##### `JWT-token`被窃取了怎么办？

自认倒霉，`sessionID`也会被窃取，`JWT-token`是存储在`localstroy`里的

###### 不过有优化方法减少被别人窃取的可能性：

给用户传两个`token`

- `access-token` 有效时间比较短，例如 5 min
- `refresh-token` 有效时间比较长，例如 14 天

每次用户在向服务器端发送请求的时候，带的是`access-token`

因为用户发送的请求有时是`Get`类型的，有时请求是`Post`类型的

- `Get`类型的请求是明文的，不是很安全
- `Post`类型的请求是安全的

用户发送的请求有时候是`Get`有时候是`Post`，所以要用有效时间短的`access-token`，当`access-token`过期的时候，会用一个`Post`类型的请求，根据`refresh-token`得到一个新的`access-token`，相当于做了一层防护罩，

用`Get`类型暴露出来的`access-token`有效时间特别短，而有效时间比较长的`refresh-token`只会用`Post`类型请求，不会暴露

此项目不采用这种优化方法，只会返回一个有效时间为 14 天的`token`

-----------------------------------

#### 配置`JWT`

`JWT`小工具：[https://jwt.io/](https://jwt.io/)

##### 在`Maven`仓库里搜索，添加依赖

- `jjwt-api`
- `jjwt-impl`
- `jjwt-jackson`

##### 实现`utils.JwtUtil`类，为`jwt`工具类，用来创建、解析`jwt-token`

- 将一个字符串加上密钥加上有效期变成加密之后的字符串
- 解析出`JWT-token`里的`userID` 

##### 实现`config.filter.JwtAuthenticationTokenFilter`类

- 验证用户发送请求时携带的`JWT-token`是否合法
- 如果合法，将`user`的信息提取到上下文中

##### 配置`config.SecurityConfig`类，放行登录、注册等接口

- 放置公开链接

---------------

#### 编写API

##### 先对数据库进行修改

- 将数据库中的`id`域变为自增
  - 在数据库中将`id`列变为自增
  - 在`pojo.User`类中添加注解：`@TableId(type = IdType.AUTO)`

- 创建一个新的列，用来存储用户的头像

##### 正式实现`API`

在`SpringBoot`里写`API`的流程：

需要写三个地方

- `Controller`， 调用`Service`

- 在`Service`里写接口
- 在`Service`的`impl`里写接口的具体实现

##### 登录验证的`API`

- 实现`/user/account/token/`：验证用户名密码，验证成功后返回`jwt-token`，公开的
  - 用户登录界面是`Post`类型的，因为要传密码
- 调试阶段，直接在网址上输入`url`访问是一个`Get`类型的请求，因此不能在浏览器里调试（error: 405）
    - 在 `postman`工具里调试
    - 在前端框架里调试，`ajax` 
  - `Authorization`授权验证
- 实现`/user/account/info/`：根据令牌返回用户信息
  - 从上下文中将信息提取出来
  - 返回信息是`Get`类型，一般来说获取信息都是`Get`，修改添加删除都是`Post` 
- 实现`/user/account/register/`：注册账号，公开的
  - 实现一些简单的业务逻辑
  - 判断用户名是否被用过时，需要注入数据库查询的接口
  - 存入数据库时，因为`id`设置了自增，所以不需要传`id`，设置为 `null`即可

-------------------------

#### 前端：用户登录和注册界面

创建页面，将路径加到`router`里

在<a href="https://v5.bootcss.com/">Bootstrap</a>找登录和注册界面的样式

- `grids`布局，将整个平面分成12格，登录放到中间3格区域，并居中
- 表单
- 确认按钮

##### 存全局信息`Vuex` 

在每个页面都要存当前登录的用户是谁，所以需要将用户信息存成全局的

将表单内容与函数变量绑定起来

##### 登录之后动态显示信息

登录成功之后，向后端再发送一个请求获取当前用户的信息

获取信息之后在导航栏显示

##### 退出登录

删除本地的`JWT-token` 

##### 如何实现限制一台设备登录

在数据库内存每个用户当前授权的`token`有几个，如果多于一个，就把前一个注销，当用户登录的时候，验证`token`是否是注销掉的`token`，如果是就删掉

##### 回调函数

执行完，会调用一个函数，即回调函数

------------------------

8/1

#### 前端的页面授权功能

未登录时，所有页面都会重定向到登录页面

这里在`router`里实现，`beforeEach()`，当每次通过`router`进入每个页面之前，执行这个函数

存一下每个页面需不需要授权

读入登录状态

-------------

#### 注册页面

与登录页面类似

因为注册不会修改任何值（state），所以注册不需要放到vuex里

--------------------

#### 登录状态的持久化

将`token`存到本地的`localstorage`（浏览器本地的一块硬盘空间）里

登录的时候将信息存到`localstorage`里

`localstorage`是一个字典

当用户访问页面时被重定向到登录页面时，实现判断有没有将`token`存到`localstorage`里，如果存在，就将这个`token`取出来，然后验证一下是不是过期了，如果没有过期，用户就不需要重新登录，并跳到首页

跳到首页时，因为是重定向的，所以会闪过登录页面，对用户体验不好

所以对前端的`API`进行优化，定义全局变量`pulling_info` 