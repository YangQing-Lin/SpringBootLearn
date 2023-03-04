### SSM整合-前后台协议联调（修改功能）

---------------------------

##### 先展示要修改的信息

```js
//弹出编辑窗口
handleUpdate(row) {
    console.log(row); // row.id 作为查询条件
    // 查询数据，根据 id 查询
    // 展示弹窗，在弹窗中加载数据
    axios.get("/books/" + row.id).then((res) => { // 获取数据
        this.formData = res.data.data; // 加载数据
        this.dialogFormVisible4Edit = true; // 显示弹窗
    });
},
```

**需要对操作的状态进行判断**

```js
//弹出编辑窗口
handleUpdate(row) {
    // row.id 作为查询条件
    // 查询数据，根据 id 查询
    // 展示弹窗，在弹窗中加载数据
    axios.get("/books/" + row.id).then((res) => { // 获取数据
        // console.log(res.data.data)
        if (res.data.code === 20041) {
            this.formData = res.data.data; // 加载数据
            this.dialogFormVisible4Edit = true; // 显示弹窗
        } else {
            this.$message.error(res.data.msg);
        }
    });
},
```

##### 编辑添加

```js
//编辑
handleEdit() {
    // 发送ajax请求
    axios.put("/books", this.formData).then((res)=>{
        // console.log(res.data);

        // 如果操作成功，关闭弹窗，显示更新之后的数据
        if (res.data.code === 20031) {
            this.dialogFormVisible4Edit = false;
            this.$message.success("修改成功");
        } else if (res.data.code === 20030) {
            this.$message.error("修改失败");
        } else {
            this.$message.error(res.data.msg);
        }
    }).finally(()=>{
        this.getAll();
    });
},
```

