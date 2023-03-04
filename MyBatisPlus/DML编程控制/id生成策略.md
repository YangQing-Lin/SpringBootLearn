### id生成策略

----------------

##### id生成策略控制

- 不同的表应用不同的id生成策略
  - 日志：自增（1，2，3，4，......）
  - 购物订单：特殊规则（FQ23948AK3843）
  - 外卖单：关联地区日期等信息（10 04 20200314 34 91）
  - 关系表：可省略id
  - ......
- **使用注解控制**
  - 名称：`@TableId`
  - 类型：属性注解
  - 位置：模型类中用于表示主键的属性定义上方
  - 作用：设置当前类中主键属性的生成策略
  - 相关属性
    - value：设置数据库主键名称
    - type：设置主键属性的生成策略，值参照IdType枚举值

##### type属性值

- AUTO(0)：使用数据库id自增策略控制id生成
- NONE(1)：不设置id生成策略
- INPUT(2)：用户手工输入id
- ASSIGN_ID(3)：雪花算法生成id（可兼容数值型与字符串型）
- ASSIGN_UUID4)：以UUID生成算法作为id生成策略

##### 雪花算法id组成

img

##### 设置所有实体类id自增策略

取代`@TableId(type = IdType.ASSIGN_ID)`

```yaml
# mybatisplus日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  # 取消mybatisplus的banner
  global-config:
    banner: false
    # 设置所有的实体类id自增策略
    db-config:
      id-type: assign_id
```

##### 设置数据库表的前缀

取代`@TableName("tbl_user")`

```yaml
# mybatisplus日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  # 取消mybatisplus的banner
  global-config:
    banner: false
    # 设置所有的实体类id自增策略
    db-config:
      id-type: assign_id
      # 设置数据库表的名字前缀
      table-prefix: tbl_
```

