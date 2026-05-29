<script setup lang="ts">
import { computed, reactive } from 'vue';
import { useAuthStore } from '@/store/modules/auth';
import { useFormRules, useNaiveForm } from '@/hooks/common/form';
import { $t } from '@/locales';

defineOptions({
  name: 'PwdLogin'
});

const authStore = useAuthStore();
const { formRef, validate } = useNaiveForm();

interface FormModel {
  userName: string;
  password: string;
}

const model: FormModel = reactive({
  userName: '',
  password: ''
});

const rules = computed<Record<keyof FormModel, App.Global.FormRule[]>>(() => {
  const { formRules } = useFormRules();

  return {
    userName: formRules.userName,
    password: formRules.pwd
  };
});

async function handleSubmit() {
  await validate();
  await authStore.login(model.userName, model.password);
}
</script>

<template>
  <NForm ref="formRef" :model="model" :rules="rules" size="large" :show-label="false" @keyup.enter="handleSubmit">
    <NFormItem path="userName">
      <NInput
        v-model:value="model.userName"
        :placeholder="$t('page.login.common.userNamePlaceholder')"
        class="login-input"
      />
    </NFormItem>
    <NFormItem path="password">
      <NInput
        v-model:value="model.password"
        type="password"
        show-password-on="click"
        :placeholder="$t('page.login.common.passwordPlaceholder')"
        class="login-input"
      />
    </NFormItem>
    <NButton
      type="primary"
      size="large"
      block
      :loading="authStore.loginLoading"
      class="login-button"
      @click="handleSubmit"
    >
      {{ $t('common.confirm') }}
    </NButton>
  </NForm>
</template>

<style scoped>
.login-input {
  height: 48px;
}

.login-button {
  height: 48px;
  margin-top: 8px;
  font-size: 16px;
}
</style>
