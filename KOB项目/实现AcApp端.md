## 实现AcApp端

2022/8/31

-----------

##### 前言

Kob是一个前后端分离的项目，一个后端可以支持前端（web，小程序，app）

web端写完之后就是一些静态文件（css，js），只要能调用这些文件的就可以运行代码

小程序，app，acapp都可以调用这些代码，实现前端

------

### 准备工作

##### 检查vue版本

在终端中输入`vue --version`，如果版本号低于@vue/cli 5.0.8，推荐重新安装该版本：

`npm i -g @vue/cli@5.0.8`

#### 修改Nginx配置

跨域问题：前端域名和后端域名不在一个域名下

##### 使其支持跨域，并且支持AcApp端访问

在acwing应用里找到配置信息，并用其更新云端的nginx配置

更新后重新加载配置：`sudo /etc/init.d/nginx reload`

-----------

### 编写acapp前端项目

检查acapp项目里有没有自动生成**vue.config.js文件**，如果没有使用5.0版本以上的vue重新创建一个acapp项目

##### 安装依赖：

- `bootstrap`
- `@properjs/core`
- `vue3-ace-editor`
- `jquery`

##### 修改配置文件`vue.config.js`

可以让vue3将项目打包成一个js文件和一个css文件

```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  configureWebpack: {
    // No need for splitting
    optimization: {
      splitChunks: false
    }
  }
})
```

##### 将web端的src复制到acapp

---------------

### 打包acapp项目

##### vue脚手架打包

将打包好的dist文件夹里的js文件和css文件传到云端（位置可以参考nginx.conf）

##### 修改js文件，并添加主类：

```js
export class Game {
  constructor(id, AcWingOS) {
    const myappid = '#' + id;
    myfunc(myappid, AcWingOS);
  }
}
```

#### 编写脚本缩短调试时间

##### 在云端acapp编写脚本rename.sh：

1. 将云端acapp里的**app.js**和**app.css**文件删掉
2. 将传来的 ***.js** 和 ***.css** 文件更名为 **app.js** 和 **app.css**

赋予脚本可执行权限：`chmod +x rename.sh`

##### 在本地acapp编写脚本upload.sh：

1. 将打包好的**js**文件和**css**文件上传到springboot服务器acapp里
2. 执行云端的**rename.sh**脚本

赋予脚本可执行权限：`chmod +x upload.sh`

在终端里执行`sh upload.sh`，每次调试的时候执行这个脚本，就可以同步本地和云端的**js**文件和**css**文件

-------------------------

### 打包和调试

#### 在本地调试

##### 每次调试前

1. 在App.vue里添加一个模拟acapp的框（<window>）
2. 在App.vue里修改<style scoped>为<style>

开始设计样式，并在本地查看效果

#### :star:每一次的打包上传云端步骤

1. 将App.vue里模拟acapp的框（<window>）删掉

2. 将App.vue里<style>修改为<style scoped>，只对自己的acapp前端有影响

3. 使用vue脚手架打包前端项目

4. 修改打包后的js文件

   ```js
   const myfunc = ...myappid, AcWingOS
   
   ...将前面引入的参数AcWingOS加入全局变量里
   
   myappid, AcWingOS
   ```

5. 在js文件里加上主类

   ```js
   export class Game {
     constructor(id, AcWingOS) {
       const myappid = '#' + id;
       myfunc(myappid, AcWingOS);
     }
   }
   ```

6. 使用本地编写好的脚本上传到云端，并实现简化名称

   `sh upload.sh`

7. 在acwing里查看应用前，需要清空缓存并刷新

8. 查看acapp的效果

#### :star2:未来上线后，如果有更新，如何让用户清空缓存呢

每次更新完，将js地址加上版本号，这样用户每次用的地址都是新的

-----------------------

### 创建acapp应用

在acwing里创建应用，填写相关信息

-------------------------

### 修改AcApp前端，适配AcWing

将前端调试信息删掉，全文搜索console.log(

在本地修改样式，并调试

##### 调试阶段在前端项目的入口默认身份

在云端获取token，粘贴到**本地acapp的前端入口**

默认身份不会退出

#### 删掉vue自带的router，自己手写router

点击app里的相关链接会更改acwing的url，这是不允许的

##### 手写router的原理

一共有这些页面 menu，pk，record，record-conten，ranklist，user-bot，每个页面都是一个组件

将这些组件全部拿到App.vue里

用一个**全局变量**来表示当前应该显示哪个页面，然后在组件后面加上`v-if` 

##### 具体实现

先将没有用的删掉：登录注册页面（后续会添加第三方授权登录），error页面，router组件，导航栏

在**store**里添加**router模块**（全局变量）

将所有组件放到**App.vue**里，加上`v-if`，通过全局变量跳转页面，实现**router**

##### 创建菜单界面

对战，对战列表，排行榜，我的Bot

设计四个按钮的样式

为按钮**绑定事件**（跳转到对应的页面），通过修改全局变量来实现页面跳转

##### 实现返回按钮

每个页面都应该可以返回菜单页面

建立一个公共组件（修改原先的**Contentfield.vue**），**设置返回菜单按钮**

将每个页面用**Contentfield**框起来

#### 现在需要将acapp里的样式全部自己手写，将bootstrap删除

发现打开之后污染了acwing网页，这是因为前端里引用了bootstrap

将App.vue里的body自带的外边距消掉

##### 改造pk界面（matchground）

定义一个matchground-field将内容都括起来

将bootstrap的痕迹全部删掉，然后手写样式

:star:改造完可以先打包上传云端查看效果，改一点执行一次检查效果，不然错误太多没有修改的欲望了

----------------

2022/9/3

### 对局列表页面前端的改造，适配AcApp端

#### 布局

用两个div包起来对局列表和分页

将bootstrap的痕迹删掉

#### 细节改造

##### ctrl + shift + c查看前端样式是否正确

由于用户名长短不一，所以需要单独左对齐双方的用户名

用户名字太长影响美观，所以把一些较长的用户名截断，截断部分用省略号代替，剩余列的宽度也固定起来

为了让数据显示清楚，加上一个白色透明背景，顺便加上圆角

表头居中

分页样式，仿照web端

-----------

### 排行榜页面改造，适配AcApp端

将bootstrap的痕迹删掉

由于排行榜页面和对战列表页面类似，所以布局方式和细节改造和对战列表页面相同

------------------------------

### 我的Bot页面改造，适配AcApp端

#### 布局

列表的css样式可以仿照对战列表

将没有用的删掉，将bootstrap的痕迹删掉

调整布局时可以把模态框先隐藏

#### 创建bot的模态框

模态框是bootstrap自带的，需要手动实现类似模态框的操作

将bootstrap的痕迹删掉

加上背景，区分其他内容，方便修改

用vue来做交互，定义变量表示是否展示创建Bot的模态框，创建：弹出模态框，取消：隐去模态框

创建bot之后模态框没有隐藏，需要将bootstrap的隐藏模态框函数换成自己的

#### 修改bot的模态框

将bootstrap的痕迹删掉，换成给自己的css样式

修改模态框的保存和取消按钮同创建bot模态框

--------------

### 录像页面修改，适配AcApp端

增加路由，点击查看录像可以跳转到对应的页面

录像页面没有返回按钮，该返回按钮需要返回上一个页面

缩小页面，适配AcApp的框

---------

### 对战页面

显示左下角和右上角的div占了空间，所以下面会有一条白框

-----------------

### 结果页面

实时居中

