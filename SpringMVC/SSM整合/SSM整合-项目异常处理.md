### SSM整合-项目异常处理

----------------

##### 项目异常分类

**特征**

- 不规范的用户行为操作产生的异常
- 规范的用户行为产生的异常
- 项目运行过程中可预计且无法避免的异常
- 编程人员未预期到的异常

**分类**

- 业务异常（BusinessException）
  - 规范的用户行为产生的异常
  - 不规范的用户行为操作产生的异常
- 系统异常（SystemException）
  - 项目运行过程中可预计且无法避免的异常
- 其他异常（Exception）
  - 编程人员未预期到的异常

##### 项目异常处理方案

- 业务异常
  - 发送给对应的消息传递给用户，提醒用户规范操作
- 系统异常
  - 发送固定消息传递给用户，安抚用户
  - *发送特定消息给运维人员，提醒维护*
  - *记录日志*
- 其他异常（Exception）
  - 发送固定消息传递给用户，安抚用户
  - *发送特定消息给编程人员，提醒维护（纳入预期范围内）*
  - *记录日志*

---------------

##### 实现方案

先将业务异常和系统异常分类造出来，出异常的时候将异常都归为这两类处理

在exception包下

- 自定义异常（分类）

  - 系统异常`SystemException`

    - 继承`RuntimeException`

      这种运行时异常出现后自动往上抛

    - 加入私有属性`code`帮助识别异常的种类

    - 根据需求选择性的覆盖构造方法

    ```java
    package org.exception;
    
    public class SystemException extends RuntimeException {
        private Integer code;
    
        public Integer getCode() {
            return code;
        }
    
        public void setCode(Integer code) {
            this.code = code;
        }
    
        public SystemException(Integer code, String message) {
            super(message);
            this.code = code;
        }
    
        public SystemException(Integer code, String message, Throwable cause) {
            super(message, cause);
            this.code = code;
        }
    }
    ```

  - 业务异常

    直接copy上面的
  
- 在可能引发异常的地方，进行异常处理或抛出（抛出自定义异常）

  - 将可能出现的异常进行包装，转换成自定义异常

    - 使用`try catch`语句进行包装

      ```java
      public Book getById(Integer id) {
          // 模拟业务异常
          if (id == 1) {
              throw new BusinessException(Code.BUSINESS_ERR, "请根据规范输入！");
          }
      
          // 将可能出现的异常进行包装，转换成自定义异常
          try {
              int i = 1/0;
          } catch (Exception e) {
              throw new SystemException(Code.SYSTEM_TIMEOUT_ERR, "服务器访问超时，请重试！", e);
          }
          return bookDao.getById(id);
      }
      
      ```
    
    这里可以抽成AOP
    
  - 定义异常专属的code码
    
      ```java
      // 异常
      public static final Integer SYSTEM_ERR = 50001;
      public static final Integer SYSTEM_TIMEOUT_ERR = 50002;
      public static final Integer SYSTEM_UNKNOW_ERR = 59999;
      public static final Integer BUSINESS_ERR = 60002;
      ```

- 拦截并处理异常（异常处理器）

  - 编写方法处理系统异常

    - 记录日志
    - 发送消息给运维
    - 发送邮件给开发人员
    - 将消息返回前端（`return new Result(...)`）

    ```java
    @ExceptionHandler(SystemException.class)
    public Result doSystemException(SystemException ex) {
        // 记录日志
        // 发送消息给运维
        // 发送邮件给开发人员，ex对象发送给开发人员
        return new Result(ex.getCode(), null, ex.getMessage());
    }
    ```

  - 编写方法处理业务异常

    ```java
    @ExceptionHandler(BusinessException.class)
    public Result d0BusinessException(BusinessException ex) {
        return new Result(ex.getCode(), null, ex.getMessage());
    }
    ```

  - 编写方法处理其他异常

    ```java
    // 其他异常
    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex) {
        // 记录日志
        // 发送消息给运维
        // 发送邮件给开发人员，ex对象发送给开发人员
        return new Result(Code.SYSTEM_UNKNOW_ERR, null, "系统繁忙，请稍后再试！");
    }
    ```

-----------

使用postman测试