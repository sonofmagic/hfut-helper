<script lang='ts' setup>
import Toast from '@vant/weapp/dist/toast/toast'
import type { AjaxResponse } from 'uni-ajax'
import img from '@/views/login/img.png'
import BaseButton from '@/components/base/BaseButton.vue'
import Tip from '@/components/base/Tip.vue'
import { loginRequest } from '@/service/api/auth'
import { useAsync } from '@/hooks/use-async'
import { useFormValidate } from '@/views/login/use-formValidate'

const { validate, form } = useFormValidate()

async function onLogin() {
  if (validate()) {
    Toast.loading({
      duration: 0,
      message: '第一次登录会花些时间，请耐心等待...\n(๑•̀ㅂ•́)و✧',
      forbidClick: true,
    })

    const { state, error } = await useAsync(loginRequest({
      username: form.username,
      password: form.password,
    }) as any)

    if (error.value) {
      Toast.fail({
        message: (error.value as AjaxResponse).data.msg,
      })
      return
    }

    if (state.value) {
      Toast.success({
        message: '登录成功',
      })
      uni.switchTab({
        url: '/views/home/home',
      })
    }
  }
}

</script>

<template>
  <van-toast id="van-toast" />
  <van-notify id="van-notify" />
  <view class="login-page">
    <view class="login-page-header">
      <img class="logo" :src="img">
    </view>
    <view class="h-[20px] w-[20px] bg-[#123456] shadow-green-500/50" />
    <div class="tip">
      <Tip :type="'info'" :content="'忘记密码的话可以去信息门户重置一下噢'" />
    </div>
    <view class="form">
      <div class="username">
        <van-field
          clearable
          type="number"
          left-icon="user-circle-o"
          placeholder="请输入学号"
          :values="form.username"
          @change="(e) => form.username = e.detail"
        />
      </div>

      <div class="password">
        <van-field
          type="password"
          left-icon="shield-o"
          placeholder="请输入信息门户密码"
          @change="(e) => form.password = e.detail"
        />
      </div>

      <view class="login-btn" @click="onLogin">
        <BaseButton />
      </view>
    </view>
  </view>
</template>

<style lang='scss' scoped>
.tip {
  margin-left: -15vw;
}
.login-page {
  width: 95vw;
  margin-left: 2.5vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  .logo {
    object-fit: cover;
  }

  .form {
    margin-top: 10px;
    width: 100%;
    .password {
      margin-top: 15px;
    }
    .login-btn {
      margin-top: 30px;
    }
  }
}

</style>
