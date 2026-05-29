<script setup lang="ts">
import { computed, reactive } from 'vue';
import { useFormRules, useNaiveForm } from '@/hooks/common/form';
import { useCaptcha } from '@/hooks/business/captcha';
import { $t } from '@/locales';

defineOptions({
  name: 'CodeLogin'
});

const { formRef, validate } = useNaiveForm();
const { label, isCounting, loading, getCaptcha } = useCaptcha();

interface FormModel {
  phone: string;
  code: string;
}

const model: FormModel = reactive({
  phone: '',
  code: ''
});

const rules = computed<Record<keyof FormModel, App.Global.FormRule[]>>(() => {
  const { formRules } = useFormRules();

  return {
    phone: formRules.phone,
    code: formRules.code
  };
});

async function handleSubmit() {
  await validate();
  window.$message?.success($t('page.login.common.validateSuccess'));
}
</script>

<template>
  <NForm ref="formRef" :model="model" :rules="rules" size="large" :show-label="false" @keyup.enter="handleSubmit">
    <NFormItem path="phone">
      <NInput v-model:value="model.phone" :placeholder="$t('page.login.common.phonePlaceholder')" class="login-input" />
    </NFormItem>
    <NFormItem path="code">
      <div class="w-full flex-y-center gap-12px">
        <NInput v-model:value="model.code" :placeholder="$t('page.login.common.codePlaceholder')" class="login-input" />
        <NButton
          size="large"
          :disabled="isCounting"
          :loading="loading"
          class="captcha-btn"
          @click="getCaptcha(model.phone)"
        >
          {{ label }}
        </NButton>
      </div>
    </NFormItem>
    <NButton type="primary" size="large" block class="login-button" @click="handleSubmit">
      {{ $t('common.confirm') }}
    </NButton>
  </NForm>
</template>

<style scoped>
.login-input {
  height: 48px;
}

.captcha-btn {
  height: 48px;
  white-space: nowrap;
}

.login-button {
  height: 48px;
  margin-top: 8px;
  font-size: 16px;
}
</style>
