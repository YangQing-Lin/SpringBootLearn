### SSM整合-表现层与前端数据传输协议定义

----------------

##### 对于前端接收的格式问题

- 增删改，`true`

- 查单条，`json数据`

- 查全部，`数组格式json数据`

  ......

##### 统一格式

前端接收数据格式

- 创建结果模型类，封装数据到data属性中
- 封装操作结果（增删改查）到code属性中
- 封装特殊消息到message（msg）属性中

##### 协议

- 设置统一数据返回结果类

  ```java
  public class Result {
      private Object data;
      private Integer code;
      private String msg;
  }
  ```

  **注意**：

  - Result类的字段并不是固定的，可以根据需要自行增减
  - 提供若干个构造方法，方便操作

