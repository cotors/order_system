<template>
    <div class="login-container">
        <div class="login-box">
            <div style="font-size: 24px;font-weight: bolder;text-align: center;width: 100%;margin-bottom: 10px;color: gray;">欢迎登录点餐系统</div>
              <el-form :model="data.form" ref="reflogin" :rules="data.rules">
                <el-form-item prop="username">
                    <el-input  :prefix-icon="User" size="large" v-model="data.form.username" placeholder="请输入用户名"/>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input :prefix-icon="Lock" size="large" v-model="data.form.password" placeholder="请输入密码" show-password/>
                </el-form-item>
                <el-form-item>
                    <el-select v-model="data.form.role" style="width: 100%;">
                        <el-option value="USER" label="用户"></el-option>
                        <el-option value="ADMIN" label="管理员"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button size="large" type="primary" style="width: 100%;" @click="login">登 录</el-button>
                </el-form-item>
                <div style="font-size: small;text-align: right;">还没账号?请<a  href="/register">注册</a></div>
              </el-form>
        </div>
    </div>
</template>

<script setup>
import { reactive, ref } from 'vue';
import {User,Lock} from '@element-plus/icons-vue'
import request from '@/utils/request.js'
import { ElMessage } from 'element-plus';
import router from '@/router/index.js'

const data = reactive({
    form:{
        username:'',
        password:'',
        role:'USER'
    },
    rules:{
        username: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 3, max: 10, message: '用户名在3-10位数之间', trigger: 'blur' },
        ],
        password: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 3, max: 10, message: '密码在3-12位数之间', trigger: 'blur' },
        ]
    }
})

const reflogin = ref('')

//点击登录触发
const login = () => {
    reflogin.value.validate((
        valid =>{
          if(valid){
             request.post('/login',data.form).then(res => {
                 if(res.code === '200'){
                    ElMessage.success('登录成功');
                    router.push('/')
                    localStorage.setItem('canteen-user',JSON.stringify(res.data))
                 }else{
                    ElMessage.error(res.msg)
                 }
             })
          }
    })).catch(error => {
        console.error(error);
    })
}
</script>

<style scoped>
 .login-container{
    height: 100vh;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    background: url('@/assets/imgs/eat.png');
    background-size: cover;
 }
 .login-box{
    width: 350px;
    padding: 50px 30px;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, .1);
    background-color: rgba(255, 255, 255, .9);
 }
</style>