<script setup>
import {Lock, User} from '@element-plus/icons-vue'
import {ref} from 'vue'
import {ElMessage} from 'element-plus'
import {loginService, registerService} from '@/api/user.js'
import {useRouter} from 'vue-router'
//导入token状态
import {useTokenStore} from '@/stores/token.js'
//控制注册与登录表单的显示， 默认显示注册
const isRegister = ref(false)
//用于注册的数据模型
const registerData = ref({
  username: '',
  password: '',
  rePassword: ''
})
//自定义确认密码的校验函数
const rePasswordValid = (rule, value, callback) => {
  if (value == null || value === '') {
    return callback(new Error('请再次确认密码'))
  } else if (registerData.value.password !== value) {
    return callback(new Error('两次输入密码不一致'))
  } else {
    callback()
  }
}
//用于注册的表单校验模型
const registerDataRules = ref({
  username: [
    {required: true, message: '请输入用户名', trigger: 'blur'},
    {min: 5, max: 16, message: '用户名的长度必须为5~16位', trigger: 'blur'}
  ],
  password: [
    {required: true, message: '请输入密码', trigger: 'blur'},
    {min: 5, max: 16, message: '密码长度必须为5~16位', trigger: 'blur'}
  ],
  rePassword: [
    {validator: rePasswordValid, trigger: 'blur'}
  ]
})
//用于注册的事件函数
const register = async () => {
  //console.log('注册...');
  let result = await registerService(registerData.value);
  ElMessage.success('注册成功!')
  // alert("注册成功");
  // alert(result.message ? result.message : "注册成功");
  // if (result.code == 0) {
  //   alert('注册成功!' + result.message)
  // } else {
  //   alert('注册失败!' + result.message)
  // }
}
//清空数据模型的数据
const clearRegisterData = () => {
  registerData.value = {
    username: '',
    old_pwd: '',
    rePassword: ''
  }
}
//调用useTokenStore得到状态
const tokenStore = useTokenStore();
const router = useRouter()
//用于登录的事件函数
const login = async () => {
  let result = await loginService(registerData.value)
  ElMessage.success('登录成功!')
  //保存token
  tokenStore.setToken(result.data)
  router.push("/")
  // alert("登录成功");
  // alert(result.message ? result.message : "登录成功");
  // if (result.code == 0) {
  //   alert('登录成功!')
  // } else {
  //   alert('登录失败!')
  // }
}
</script>

<template>
  <el-row class="login-page">
    <el-col :span="12" class="bg"></el-col>
    <el-col :span="6" :offset="3" class="form">
      <!-- 注册表单 -->
      <el-form ref="form" size="large" autocomplete="off" v-if="isRegister" :model="registerData"
               :rules="registerDataRules">
        <el-form-item>
          <h1>注册</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input :prefix-icon="User" placeholder="请输入用户名" v-model="registerData.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input :prefix-icon="Lock" type="password" placeholder="请输入密码"
                    v-model="registerData.password"></el-input>
        </el-form-item>
        <el-form-item prop="rePassword">
          <el-input :prefix-icon="Lock" type="password" placeholder="请输入再次密码"
                    v-model="registerData.rePassword"></el-input>
        </el-form-item>
        <!-- 注册按钮 -->
        <el-form-item>
          <el-button class="button" type="primary" auto-insert-space @click="register">
            注册
          </el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = false;clearRegisterData()">
            ← 返回
          </el-link>
        </el-form-item>
      </el-form>
      <!-- 登录表单 -->
      <el-form ref="form" size="large" autocomplete="off" v-else :model="registerData" :rules="registerDataRules">
        <el-form-item>
          <h1>登录</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input :prefix-icon="User" placeholder="请输入用户名" v-model="registerData.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input name="password" :prefix-icon="Lock" type="password" placeholder="请输入密码"
                    v-model="registerData.password"></el-input>
        </el-form-item>
        <el-form-item class="flex">
          <div class="flex">
            <el-checkbox>记住我</el-checkbox>
            <el-link type="primary" :underline="false">忘记密码？</el-link>
          </div>
        </el-form-item>
        <!-- 登录按钮 -->
        <el-form-item>
          <el-button class="button" type="primary" auto-insert-space @click="login">登录</el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = true;clearRegisterData()">
            注册 →
          </el-link>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<style lang="scss" scoped>
/* 样式 */
.login-page {
  height: 100vh;
  background-color: #fff;

  .bg {
    background: url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
    url('@/assets/login_bg.jpg') no-repeat center / cover;
    border-radius: 0 20px 20px 0;
  }

  .form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    user-select: none;

    .title {
      margin: 0 auto;
    }

    .button {
      width: 100%;
    }

    .flex {
      width: 100%;
      display: flex;
      justify-content: space-between;
    }
  }
}
</style>

