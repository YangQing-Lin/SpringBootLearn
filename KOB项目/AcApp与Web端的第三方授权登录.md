## AcApp与Web端的第三方授权登录

2022/9/7

-----------------

以AcWing为例

#### 前期准备

##### 添加依赖

[Maven仓库](https://mvnrepository.com/)

在pom.xml中添加依赖`httpclient`，**向acwing发送请求**

在SpringBoot中添加**redis**依赖：`spring-boot-starter-data-redis`

##### 添加工具类`service.impl.user.account.acwing.utils.HttpClientUtil`

发送http请求的封装

传入两个参数，url和url的参数，然后该类会向这个url发送一个请求，将返回结果变成一个字符串返回

##### 在数据库的user表中添加列openid：

在云端同样如此

`sudo mysql -u root -p[passwd]`，进入数据库

在kob数据库里添加openid列：`alter table user add column openid varchar(200);`

###### :star2:每次修改数据库之后一定要记得改后端的pojo，并且更新相关的接口

#### :star:Redis

相当于map

需要先安装redis服务

##### 注入redis的接口

##### 存入redis

```java
redisTemplate.opsForValue().set(state.toString(), "true"); // 将state存入redis
```

##### 设置过期时间

```java
redisTemplate.expire(state.toString(), Duration.ofMillis(10)); // 设置过期时间，这里是10分钟
```

##### 启动redis服务

`sudo redis-server /etc/redis/redis.conf`

##### 登录redis

`redis-cli`

--------------------------------

### AcApp端AcWing一键登录

#### 具体流程

### 实现AcApp端第三方登录的后端

定义接口`AcAppService`

- `applyCode()`申请回调URL，**返回下面第一步向acwing申请时需要用的参数**，辅助函数

  state需要加入redis中

- `receiveCode(String code, String state)`回调URL，接收服务器发送结果

  :star2::star2::star2:

  - 如果acwing**返回的state在redis里存在**，则删掉保证安全且**获取授权码成功**，若不存在获取授权码失败

  - 授权码获取成功之后，**通过下面第二步的Url向acwing申请授权令牌access_token和用户的openid**

  - 通过**HttpClientUtil和相关参数**获取**下面第二步的结果**（授权令牌access_token和用户的openid）之后，从中解析出一个`JSONObject`，再从这里解析出**授权令牌access_token和用户的openid**
  
  - 拿到openid后，在数据库里判断此openid是否存在
  
    如果存在，就取出，**并获取该用户的jwt-token，然后返回结果**（jwt-token）
  
    ##### 如果不存在，就要通过下面第三步向acwing申请用户信息
  
    - 同样的，通过**HttpClientUtil和相关参数**获取第三步Url的结果（用户信息）
    - 得到acwing的用户信息之后，判断一下用户名是否为空或者**重复**，**在kob里注册一个新User**，并获取该用户的**jwt-token**
    - 返回结果（jwt-token）

实现接口

实现对应controller

在security里放行controller的url，因为用户登录前不可能有token的，所以先放行第三方登录的相关url

### 实现AcApp端第三方登录的前端

##### 获取打开acapp的acwing的账号的信息

将AcWingOS加到全局变量里，初始没有值

1. 通过后端url（apply()）获取各种需要的参数

2. 通过AcWingOS（实现acapp端时加入的主类）的API申请第三方登录

3. ##### 实现callback()，其传入参数，就是后端APIreceiveCode()的返回值（jwt-token）

   如果失败，就关闭窗口

   如果成功，就调用之前的成功逻辑，不要将jwt-token存到浏览器的localstoryage

### 具体实现细节

#### 第一步 申请授权码code

请求授权码的API：

`AcWingOS.api.oauth2.authorize(appid, redirect_uri, scope, state, callback);`

参数在通过apply函数获取

##### 参数说明

| 参数         | 是否必须 | 说明                                                         |
| ------------ | -------- | ------------------------------------------------------------ |
| appid        | 是       | 应用的唯一id，可以在AcWing编辑AcApp的界面里看到              |
| redirect_uri | 是       | 接收授权码的地址。需要用对链接进行编码：Python3中使用urllib.parse.quote；Java中使用URLEncoder.encode |
| scope        | 是       | 申请授权的范围。目前只需填userinfo                           |
| state        | 是       | 用于判断请求和回调的一致性，授权成功后后原样返回。该参数可用于防止csrf攻击（跨站请求伪造攻击），建议第三方带上该参数，可设置为简单的随机数（如果是将第三方授权登录绑定到现有账号上，那么推荐用随机数 + user_id作为state的值，可以有效防止CSRF攻击） |
| callback     | 是       | redirect_uri返回后的回调函数                                 |

##### :star:state保证安全

acapp向acwing发送这些参数之后，会将state参数用redis存下来，然后设置一个有效期

acwing在给acapp返回结果的时候，会将state也一并返回，

acapp将其对比redis里存下来的state是否一致，保证安全，因为回调函数（`receiveCode`）的Url是公开的

##### 返回说明

用户同意授权后，Acwing会将code和state传递给redirect_uri

如果用户拒绝授权，则将会收到如下错误码：

```java
{
    errcode: "40010"
    errmsg: "user reject"
}
```

#### 第二步 申请授权令牌access_token和用户的openid

**请求地址**：https://www.acwing.com/third_party/api/oauth2/access_token/

参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/oauth2/access_token/?appid=APPID&secret=APPSECRET&code=CODE

##### 参数说明

| 参数   | 是否必须 | 说明                                            |
| ------ | -------- | ----------------------------------------------- |
| appid  | 是       | 应用的唯一id，可以在AcWing编辑AcApp的界面里看到 |
| secret | 是       | 应用的秘钥，可以在AcWing编辑AcApp的界面里看到   |
| code   | 是       | 第一步中获取的授权码                            |

##### 返回说明

申请成功示例：

```java
{ 
    "access_token": "ACCESS_TOKEN", 
    "expires_in": 7200, 
    "refresh_token": "REFRESH_TOKEN",
    "openid": "OPENID", 
    "scope": "SCOPE",
}
```

申请失败示例：

```java
{
    "errcode": 40001,
    "errmsg": "code expired",  # 授权码过期
}
```

##### 返回参数说明

| 参数          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| access_token  | 授权令牌，有效期2小时                                        |
| expires_in    | 授权令牌还有多久过期，单位（秒）                             |
| refresh_token | 用于刷新access_token的令牌，有效期30天                       |
| openid        | 用户的id。每个AcWing用户在每个acapp中授权的openid是唯一的,可用于识别用户。 |
| scope         | 用户授权的范围。目前范围为userinfo，包括用户名、头像         |

##### 刷新access_token的有效期

access_token的有效期为2小时，时间较短。refresh_token的有效期为30天，可用于刷新access_token

刷新结果有两种：

1. 如果access_token已过期，则生成一个新的access_token。
2. 如果access_token未过期，则将当前的access_token的有效期延长为2小时。

##### 参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/oauth2/refresh_token/?appid=APPID&refresh_token=REFRESH_TOKEN

返回结果的格式与申请access_token相同。

#### 第三步 申请用户信息

**请求地址**：https://www.acwing.com/third_party/api/meta/identity/getinfo/

参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/meta/identity/getinfo/?access_token=ACCESS_TOKEN&openid=OPENID

##### 参数说明

| 参数         | 是否必须 | 说明                     |
| ------------ | -------- | ------------------------ |
| access_token | 是       | 第二步中获取的授权令牌   |
| openid       | 是       | 第二步中获取的用户openid |

##### 返回说明

申请成功示例：

```java
{
    'username': "USERNAME",
    'photo': "https:cdn.acwing.com/xxxxx"
}
```

申请失败示例：

```java
{
    'errcode': "40004",
    'errmsg': "access_token expired"  # 授权令牌过期
}
```

---------------------------------------------

### Web端AcWing一键登录

#### 具体流程

### 实现Web端AcWing一键登录的后端

定义接口WebService，**接口逻辑基本与AcAppService一样**，下面只说不同点

- applyCode()

  **注意回调地址中将acapp改为web**且**改为前端链接（去掉"/api"）**，因为这里回调函数返回到后端，后端是restful的格式，不能重定向

  **:star:为什么去掉""/api"就是前端链接？**

  因为在nginx配置里对url做了处理，不带有"/api"就是路由到前端

  按照下面第一步申请授权码的Url，为其添加参数（字符串拼接），并返回整个请求连接

- receiveCode()

实现接口

实现controller

### 实现Web端AcWing一键登录的前端

##### 遗留bug修复

bug：当页面比较窄的时候，导航栏部分内容就消失了

解决：在bootstrap里找到合适的导航栏

##### 在注册页面加入一键登录AcAwing的按钮

在对应的位置上加入AcWing图标，[图片地址](https://cdn.acwing.com/media/article/image/2022/09/06/1_32f001fd2d-acwing_logo.png) 

修改样式

绑定函数，acwing_login，获取参数后重定向到apply_code_url

##### 实现回调路径，调用后端的receive_code()函数

加入组件，UserAccountAcWingWebReceiveCodeView.vue，并在路由里加入组件

##### 从后端receive_code函数获取jwt-token之后，可以参照登录成功获得jwt-token后的操作

### :star2:Web端前端修改之后调试流程

1. 在vue脚手架里打包
2. 找到web前端的路径（dist）
3. 将整个文件夹传到云端，`scp -r * springboot:kob/web/`，递归上传

然后就可以用域名查看效果啦

### 具体实现细节

#### 第一步 申请授权码code

**请求地址：**https://www.acwing.com/third_party/api/oauth2/web/authorize/

参数在apply函数里获取，仿照下面的示例加入参数

##### 参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/oauth2/web/authorize/?appid=APPID&redirect_uri=REDIRECT_URI&scope=SCOPE&state=STATE

##### 参数说明

| 参数         | 是否必须 | 说明                                                         |
| ------------ | -------- | ------------------------------------------------------------ |
| appid        | 是       | 应用的唯一id，可以在AcWing编辑AcApp的界面里看到              |
| redirect_uri | 是       | 接收授权码的地址。需要用对链接进行编码：Python3中使用urllib.parse.quote；Java中使用URLEncoder.encode |
| scope        | 是       | 申请授权的范围。目前只需填userinfo                           |
| state        | 是       | 用于判断请求和回调的一致性，授权成功后后原样返回。该参数可用于防止csrf攻击（跨站请求伪造攻击），建议第三方带上该参数，可设置为简单的随机数（如果是将第三方授权登录绑定到现有账号上，那么推荐用随机数 + user_id作为state的值，可以有效防止CSRF攻击） |

##### :star:state保证安全

acapp向acwing发送这些参数之后，会将state参数用redis存下来，然后设置一个有效期

acwing在给acapp返回结果的时候，会将state也一并返回，

acapp将其对比redis里存下来的state是否一致，保证安全，因为回调函数（`receiveCode`）的Url是公开的

##### 返回说明

用户同意授权后会**重定向到redirect_uri**，返回参数为code和state。链接格式如下：

```java
redirect_uri?code=CODE&state=STATE
```

如果用户拒绝授权，则不会发生重定向。

#### 第二步 申请授权令牌access_token和用户的openid

**请求地址**：https://www.acwing.com/third_party/api/oauth2/access_token/

参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/oauth2/access_token/?appid=APPID&secret=APPSECRET&code=CODE

##### 参数说明

| 参数   | 是否必须 | 说明                                            |
| ------ | -------- | ----------------------------------------------- |
| appid  | 是       | 应用的唯一id，可以在AcWing编辑AcApp的界面里看到 |
| secret | 是       | 应用的秘钥，可以在AcWing编辑AcApp的界面里看到   |
| code   | 是       | 第一步中获取的授权码                            |

##### 返回说明

申请成功示例：

```java
{ 
    "access_token": "ACCESS_TOKEN", 
    "expires_in": 7200, 
    "refresh_token": "REFRESH_TOKEN",
    "openid": "OPENID", 
    "scope": "SCOPE",
}
```

申请失败示例：

```java
{
    "errcode": 40001,
    "errmsg": "code expired",  # 授权码过期
}
```

##### 返回参数说明

| 参数          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| access_token  | 授权令牌，有效期2小时                                        |
| expires_in    | 授权令牌还有多久过期，单位（秒）                             |
| refresh_token | 用于刷新access_token的令牌，有效期30天                       |
| openid        | 用户的id。每个AcWing用户在每个acapp中授权的openid是唯一的,可用于识别用户。 |
| scope         | 用户授权的范围。目前范围为userinfo，包括用户名、头像         |

##### 刷新access_token的有效期

access_token的有效期为2小时，时间较短。refresh_token的有效期为30天，可用于刷新access_token

刷新结果有两种：

1. 如果access_token已过期，则生成一个新的access_token。
2. 如果access_token未过期，则将当前的access_token的有效期延长为2小时。

##### 参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/oauth2/refresh_token/?appid=APPID&refresh_token=REFRESH_TOKEN

返回结果的格式与申请access_token相同。

#### 第三步 申请用户信息

**请求地址**：https://www.acwing.com/third_party/api/meta/identity/getinfo/

参考示例：

请求方法：GET
https://www.acwing.com/third_party/api/meta/identity/getinfo/?access_token=ACCESS_TOKEN&openid=OPENID

##### 参数说明

| 参数         | 是否必须 | 说明                     |
| ------------ | -------- | ------------------------ |
| access_token | 是       | 第二步中获取的授权令牌   |
| openid       | 是       | 第二步中获取的用户openid |

##### 返回说明

申请成功示例：

```java
{
    'username': "USERNAME",
    'photo': "https:cdn.acwing.com/xxxxx"
}
```

申请失败示例：

```java
{
    'errcode': "40004",
    'errmsg': "access_token expired"  # 授权令牌过期
}
```

--------------------------------------------

### :star2:kob后端修改后如何调试？

修改完**后端**代码后

1. 在Maven里打个包，package
2. 找到打好包的文件路径，target
3. 将`.jar`文件传到云端，`scp backend-0.0.1-SNAPSHOT.jar springboot:kob/backendcloud/`
4. 传完之后将云端的项目重启，`java -jar [相关文件]`

