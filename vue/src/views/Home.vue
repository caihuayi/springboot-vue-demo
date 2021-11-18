<template>
  <div style="padding: 10px">
<!--    功能区-->
    <div style="margin:10px 0">
      <el-button type="primary" @click="add">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
    </div>
<!--    搜索区-->
    <div style="margin:10px 0">
      <el-input v-model="search" placeholder="请输入关键字" style="width: 20%" clearable></el-input>
      <el-button type="primary" style="margin-left: 5px" @click="load">查询</el-button>
    </div>
    <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%" >
      <el-table-column prop="id" label="ID" width="180" sortable />
      <el-table-column prop="username" label="用户名" width="180" />
      <el-table-column prop="nickName" label="昵称"  />
      <el-table-column prop="age" label="年龄"  />
      <el-table-column prop="sex" label="性别"  />
      <el-table-column prop="address" label="地址"  />
      <el-table-column fixed="right" label="操作" width="120">
        <template #default="scope">
          <el-button type="text" @click="handleEdit(scope.row)">编辑</el-button>
<!--          <el-button type="text" size="small" @click="handleDelete(scope.row)">删除</el-button>-->
          <el-popconfirm
              confirm-button-text="确认"
              cancel-button-text="不，谢谢"
              :icon="InfoFilled"
              icon-color="red"
              title="这一段内容确定要删除吗？"
              @confirm="handleDelete(scope.row.id)"
          >
            <template #reference>
              <el-button type="text">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <div style="margin: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="crrentPage"
          :page-sizes="[5, 10, 20]"
          :page-size="pageSize"
          :pager-count="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
      >
      </el-pagination>

      <el-dialog
          v-model="dialogVisible"
          title="提示"
          width="30%"
      >
        <el-form :model="form" label-width="120px">
          <el-form-item label="用户名">
            <el-input v-model="form.username" style="width: 80px"></el-input>
          </el-form-item>
          <el-form-item label="昵称">
            <el-input v-model="form.nickName" style="width: 80px"></el-input>
          </el-form-item>
          <el-form-item label="年龄">
            <el-input v-model="form.age" style="width: 80px"></el-input>
          </el-form-item>
          <el-form-item label="性别">
            <el-radio v-model="form.sex" label="男">男</el-radio>
            <el-radio v-model="form.sex" label="女">女</el-radio>
            <el-radio v-model="form.sex" label="未知">未知</el-radio>
          </el-form-item>
          <el-form-item label="地址">
            <el-input type="textarea" v-model="form.address" style="width: 80px"></el-input>
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取消</el-button>
            <el-button type="primary" @click="save">确定</el-button>
      </span>
        </template>
      </el-dialog>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

import request from "@/util/request";

export default {
  name: 'Home',
  components: {

  },
  data() {
    return {
      crrentPage: 1,
      form: {},
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize:10,
      total: 10,
      tableData: [
      ],
    }
  },
  created(){
    this.load()
  },
  methods:{
    load(){
      request.get("/api/user", {
        params: {
          pageNum: this.currentPage,
          pageSize: this.pageSize,
          search: this.search
        }
      }).then(res => {
        console.log(res)
        this.tableData = res.data.records
        this.total = res.data.total
      })
    },
    add(){
      this.dialogVisible = true;
      this.form = {}
    },
    save() {
      if (this.form.id) {
        request.put("/api/user", this.form).then(res => {
          console.log(res)
          if (res.code === '0') {
            this.$message({
              type: "success",
              message: "更新成功"
            })
          } else {
            this.$message({
              type: "error",
              message: res.msg
            })
          }
        })
      } else {  //新增
        request.post("/api/user", this.form).then(res => {
          console.log(res)
          if (res.code === '0') {
            this.$message({
              type: "success",
              message: "新增成功"
            })
          } else {
            this.$message({
              type: "error",
              message: res.msg
            })
          }
        })
      }
      this.load()
      this.dialogVisible = false
    },
    handleEdit(row){
      this.form = JSON.parse(JSON.stringify(row))
      //console.log("Edit Click!")
      this.dialogVisible = true
    },
    handleDelete(id){
      request.delete("/api/user/" + id).then(res => {
        if (res.code === '0'){
          this.$message({
            type: "success",
            message: "删除成功"
          })
        }else{
          this.$message({
            type: "error",
            message: res.msg
          })
        }
        this.load() //重新加载表格数据
      })
    },
    handleSizeChange(pageSize){ //改变当前每页个数触发
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum){  //改变当前页码触发
      this.currentPage = pageNum
      this.load()
    }
  },
}
</script>
