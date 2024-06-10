<template>
    <div class="login-container">
        <div class="login-box">
            <div style="font-size: 24px;font-weight: bolder;text-align: center;width: 100%;margin-bottom: 10px;color: gray;">欢迎登录点餐系统</div>
              <el-form :model="data.form" ref="refRegister" :rules="data.rules">
                <el-form-item prop="username">
                    <el-input  :prefix-icon="User" size="large" v-model="data.form.username" placeholder="请输入用户名"/>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input :prefix-icon="Lock" size="large" v-model="data.form.password" placeholder="请输入密码" show-password/>
                </el-form-item>
                <el-form-item prop="rePassword">
                    <el-input :prefix-icon="Lock" size="large" v-model="data.form.rePassword" placeholder="请再次输入密码" show-password/>
                </el-form-item>     
                <el-form-item>
                    <el-button size="large" type="primary" style="width: 100%;" @click="register">注 册</el-button>
                </el-form-item>
                <div style="font-size: small;text-align: right;">已有账号?请<a href="/login">登录</a></div>
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

const checkRePassword = (rule,value,collback)=>{
    if(value === ''){
        return new Error('密码为空')
    }else if(value != data.form.password){
        return new Error('两次密码不一致')
    }else{
        collback
    }
}

const data = reactive({
    form:{
        username:'',
        password:'',
        rePassword:'',
        role:'USER'
    },
    rules:{
        username: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 3, max: 10, message: '用户名在3-10位数之间', trigger: 'blur' }
        ],
        password: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 3, max: 10, message: '密码在3-12位数之间', trigger: 'blur' }
        ],
        rePassword:[
           { validator:checkRePassword, trigger: 'blur' }
        ]
    }
})

const refRegister = ref()

//点击注册触发
const register = () => {
    //方法一:element-plus校验规则
    // refRegister.value.validate((
    //     valid =>{
    //       if(valid){
    //          request.post('/register',data.form).then(res => {
    //              if(res.code === '200'){
    //                 ElMessage.success('注册成功')
    //                 router.push('/login')
    //              }else{
    //                 ElMessage.error(res.msg)
    //              }
    //          })
    //       }
    // })).catch(error => {
    //     console.error(error);
    // })

    //方法二: if判断校验
    if(data.form.username === ''){
        ElMessage.error('用户名不能为空')
    }else if(data.form.username.length < 3 || data.form.username.length > 10){
        ElMessage.error('长度在3-10位数之间')
    }else if(data.form.password.length < 3 || data.form.password.length > 12){
        ElMessage.error('密码长度在3-12位数之间')
    }else if(data.form.password.length === ''){
        ElMessage.error('密码不能为空')
    }else if(data.form.rePassword.length === ''){
        ElMessage.error('确认密码不能为空')
    }else if(data.form.rePassword != data.form.password){
        ElMessage.error('两次密码不一致')
    }else{
        request.post('/register',data.form).then(res => {
        ElMessage.success('注册成功')
        router.push('/login')
    })
    }
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