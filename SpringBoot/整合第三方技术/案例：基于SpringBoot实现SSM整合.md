### 案例：基于SpringBoot实现SSM整合

---------

1. pom.xml

   配置起步依赖，必要的资源坐标（druid）

2. application.yml

   设置数据源、端口等

3. 配置类

   全部删除

4. dao

   设置@Mapper

5. 测试类

6. 页面

   放置在resources目录下的static目录中

   设置主页static/index.html

   ```js
   <script>
       document.location.href="pages/books.html";
   </script>
   ```

   