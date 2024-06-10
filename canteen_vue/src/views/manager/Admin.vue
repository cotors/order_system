<template>
  <div>

    <div class="card" style="margin-bottom: 10px;">
      <el-input prefix-icon="Search" style="width: 300px; margin-right: 10px" placeholder="请输入名称查询" v-model="data.name"></el-input>
      <el-button type="primary" @click="load">查询</el-button>
      <el-button type="info" style="margin: 0 10px" @click="reset">重置</el-button>
    </div>

    <div class="card" style="margin-bottom: 10px">
      <div style="margin-bottom: 10px">
        <el-button type="primary" @click="visibale();title='添加用户'">新增</el-button>
      </div>
      <el-table :data="data.tableData" style="width: 100%">
        <el-table-column prop="id" label="序号" width="70"/>
        <el-table-column prop="username" label="账号"/>
        <el-table-column prop="name" label="名称"/>
        <el-table-column prop="avatar" label="头像">
          <template #default="scope">
            <img v-if="scope.row.avatar" :src="scope.row.avatar" style="width: 50px;height: 50px;border-radius: 50%;" />
          </template>
        </el-table-column>
        <el-table-column prop="role" label="角色"/>
        <el-table-column label="操作" width="180">
          <template #default="scope">
            <!-- scoped.row:获取本行数据信息 -->
            <el-button type="primary" @click="Edit(scope.row);title='编辑用户'">编辑</el-button>
            <el-button type="danger" @click="deleteById(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 弹窗 -->
    <el-dialog v-model="data.formVisible" :title="title" width="500" destroy-on-close>
    <el-form :model="data.form" style="padding: 0px 30px;">
      <el-form-item label="账号:">
        <el-input v-model="data.form.username" autocomplete="off" :disabled="!!data.form.id"/>
      </el-form-item>
      <el-form-item label="用户名:">
        <el-input v-model="data.form.name" autocomplete="off"/>
      </el-form-item>
      <el-form-item>
         <el-upload action="http://localhost:9090/files/upload" :on-success="uploadAvatar" class="avatar-uploader">
              <img v-if="data.form.avatar" :src="data.form.avatar" class="avatar" />
              <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
         </el-upload>
      </el-form-item>
    </el-form>
    <template #footer>
      <div>
        <el-button @click="data.formVisible = false">取消</el-button>
        <el-button type="primary" @click="title==='添加用户'?save():update()">提交</el-button>
      </div>
    </template>
  </el-dialog>

    <div class="card" v-if="data.total">
      <el-pagination background layout="prev, pager, next" @current-change="load" v-model:page-size="data.pageSize" v-model:current-page="data.pageNum" :total="data.total"/>
    </div>

  </div> 
</template>

<script setup>
import {reactive,ref} from "vue"
import request from '@/utils/request.js'
import { ElMessage,ElMessageBox  } from "element-plus";

const data = reactive({
  tableData: [],
  total: 0,
  pageNum: 1,  // 当前的页码
  pageSize: 5,  // 每页的个数
  formVisible: false,
  form: {},
  name:'',
  title:''
})


const load = () => {
   request.get('/admin/selectPage',{
    params:{
      pageNum:data.pageNum,
      pageSize:data.pageSize,
      name:data.name
    }
   }).then(res => {
      data.tableData = res.data.list
      data.total = res.data.total
   })
}

load()

const reset = () => {
  data.name = ''
  load()
}

const visibale = () => {
  data.form = {} //初始化数据，避免显现上次添加的数据信息
  data.formVisible  = true
}

const save = () => {
  request.post('/admin/add',data.form).then(res => {
    if(res.code === '200'){
       ElMessage.success('添加成功')
       data.formVisible = false
       load()
    }else{
       ElMessage.error(res.msg)
    }
  })
}

const Edit = (row) => {
   data.form.username = row.username
   data.form.name = row.name
   data.form.id = row.id
   data.form.avatar = row.avatar
   data.formVisible = true
}

const update = () => {
  request.put('/admin/update',data.form).then(res => {
    if(res.code === '200'){
      ElMessage.success('更新成功')
      data.formVisible = false
      load()
    }else{
      ElMessage.error(res.msg)
    }
  })
}

//删除
const deleteById = (row) => {
  ElMessageBox.confirm(
    '您是否要删除该管理员用户?',
    '温馨提示',
    {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning',
    }
  )
    .then(() => {
      request.delete('/admin/delete/'+ row.id).then(res => {
        if(res.code === '200'){
          ElMessage({
          type: 'success',
          message: '删除成功',
          })
          load()
        }else{
          ElMessage.error(res.msg)
        }
      })
     
    })
    .catch(() => {
      ElMessage({
        type: 'info',
        message: '取消删除',
      })
    })
}

const uploadAvatar = (file) => {
   data.form.avatar = file.data
}

const emit = defineEmits(["updateUser"])

const user = JSON.stringify(localStorage.getItem('canteen-user'))

emit("updateUser",data.form)



</script>

<style scoped>
.avatar-uploader .avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  text-align: center;
}
</style>