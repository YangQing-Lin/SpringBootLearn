### SSM整合-前后台协议联调（添加功能）

---------------

##### 添加功能

如果操作成功，关闭弹窗，显示更新之后的数据

```js
//添加
handleAdd () {
    // 发送ajax请求
    axios.post("/books", this.formData).then((res)=>{
        // 如果操作成功，关闭弹窗，显示更新之后的数据
        this.dialogFormVisible = false;
        this.getAll();
    });
},
```