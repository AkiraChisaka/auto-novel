<script lang="ts" setup>
import { FormInst, FormItemRule, FormRules } from 'naive-ui';

import { Locator, formatError } from '@/data';

const loadingBar = useLoadingBar();
const message = useMessage();

const formRef = ref<FormInst>();

const formValue = ref({
  emailOrUsername: '',
  password: '',
});

const formRules: FormRules = {
  emailOrUsername: [
    {
      validator: (_rule: FormItemRule, value: string) => value.length > 0,
      message: '邮箱/用户名不能为空',
      trigger: 'input',
    },
  ],
  password: [
    {
      validator: (_rule: FormItemRule, value: string) => value.length >= 8,
      message: '密码至少为8个字符',
      trigger: 'input',
    },
  ],
};

const signIn = async () => {
  try {
    await formRef.value?.validate();
  } catch (e) {
    return;
  }
  loadingBar.start();
  try {
    await Locator.authRepository().signIn(formValue.value);
    loadingBar.finish();
  } catch (e) {
    loadingBar.error();
    message.error('登录失败:' + (await formatError(e)));
  }
};
</script>

<template>
  <n-form
    ref="formRef"
    :model="formValue"
    :rules="formRules"
    label-placement="left"
    label-width="auto"
  >
    <n-form-item-row path="emailOrUsername">
      <n-input
        v-model:value="formValue.emailOrUsername"
        placeholder="用户名/邮箱"
        :input-props="{ spellcheck: false }"
      />
    </n-form-item-row>
    <n-form-item-row path="password">
      <n-input
        v-model:value="formValue.password"
        type="password"
        show-password-on="click"
        placeholder="密码"
        @keyup.enter="signIn()"
      />
    </n-form-item-row>
  </n-form>

  <c-a :to="{ name: 'reset-password' }">忘记密码</c-a>
  <n-button type="primary" block @click="signIn" style="margin-top: 20px">
    登录
  </n-button>
</template>
