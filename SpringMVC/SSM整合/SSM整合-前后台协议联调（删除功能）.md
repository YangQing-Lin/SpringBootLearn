### SSM整合-前后台协议联调（删除功能）

----------------

1. **弹出提示框**
2. **做删除业务**
3. **取消删除操作**
4. **完成操作之后刷新页面**

```js
// 删除
handleDelete(row) {
    // 弹出提示框
    this.$confirm("此操作永久删除当前数据，是否确定？", "提示", {
        type:'info'
    }).then(()=>{ // 点击确定
        // 删除业务
        axios.delete("/books/" + row.id).then((res) => {
            if (res.data.code === 20021) {
                this.$message.success("删除成功");
            } else {
                this.$message.error("删除失败");
            }
        });
    }).catch(()=>{ // 点击取消
        // 取消删除
        this.$message.info("取消成功");
    }).finally(()=>{
        this.getAll();
    });
}
```

