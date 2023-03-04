### SpringBoot程序快速启动

--------------------

##### 前后端分离

现在后端人员可以将SpringBoot程序打包发给前端人员，直接运行，只需要连接对应的数据库

##### SpringBoot项目快速启动

1. 对SpringBoot项目打包（执行Maven构建指令package）
2. 执行启动指令`java -jar [springboot.jar]`

##### 注意：

jar支持命令行启动需要依赖maven插件支持，请确认打包时是否具有SpringBoot对应的maven插件

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

