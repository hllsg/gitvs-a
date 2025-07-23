<template>
  <div class="login-container">
    <a-card title="工单管理系统登录" style="width: 350px; margin: 100px auto;">
      <a-form @submit.prevent="onSubmit" :model="form" :label-col="{style: {width: '60px'}}">
        <a-form-item label="用户名">
          <a-input v-model:value="form.username" placeholder="请输入用户名" />
        </a-form-item>
        <a-form-item label="密码">
          <a-input type="password" v-model:value="form.password" placeholder="请输入密码" />
        </a-form-item>
        <a-form-item>
          <a-button type="primary" html-type="submit" block @click="onSubmit">登录</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </div>
</template>

<script setup>
import { reactive } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const form = reactive({ username: '', password: '' });

function onSubmit() {
  if (!form.username) {
    message.error('请输入用户名');
    return;
  }
  // 简单模拟权限
  localStorage.setItem('role', form.username === 'admin' ? 'admin' : 'user');
  router.push('/main');
}
</script>

<style scoped lang="less">
.login-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f0f2f5;
}
</style>
