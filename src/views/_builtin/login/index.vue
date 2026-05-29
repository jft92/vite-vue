<script setup lang="ts">
import { computed, ref } from 'vue';
import type { Component } from 'vue';
import { loginModuleRecord } from '@/constants/app';
import { useAppStore } from '@/store/modules/app';
import { useThemeStore } from '@/store/modules/theme';
import { $t } from '@/locales';
import PwdLogin from './modules/pwd-login.vue';
import CodeLogin from './modules/code-login.vue';

const appStore = useAppStore();
const themeStore = useThemeStore();

interface LoginModule {
  label: App.I18n.I18nKey;
  component: Component;
}

type LoginModuleKey = 'pwd-login' | 'code-login';

const moduleMap: Record<LoginModuleKey, LoginModule> = {
  'pwd-login': { label: loginModuleRecord['pwd-login'], component: PwdLogin },
  'code-login': { label: loginModuleRecord['code-login'], component: CodeLogin }
};

const activeModuleKey = ref<LoginModuleKey>('pwd-login');
const activeModule = computed(() => moduleMap[activeModuleKey.value]);

function handleTabClick(key: LoginModuleKey) {
  activeModuleKey.value = key;
}
</script>

<template>
  <div class="login-page">
    <div class="login-container">
      <!-- Logo 和标题 -->
      <div class="login-header">
        <SystemLogo class="login-logo" />
        <h1 class="login-title">{{ $t('system.title') }}</h1>
      </div>

      <!-- 登录模块切换 -->
      <div class="login-tabs">
        <button
          v-for="(item, key) in moduleMap"
          :key="key"
          class="login-tab"
          :class="{ active: activeModuleKey === key }"
          @click="handleTabClick(key)"
        >
          {{ $t(item.label) }}
        </button>
      </div>

      <!-- 登录表单 -->
      <div class="login-form">
        <Transition :name="themeStore.page.animateMode" mode="out-in" appear>
          <component :is="activeModule.component" />
        </Transition>
      </div>

      <!-- 底部主题切换 -->
      <div class="login-footer">
        <ThemeSchemaSwitch
          :theme-schema="themeStore.themeScheme"
          :show-tooltip="false"
          @switch="themeStore.toggleThemeScheme"
        />
        <LangSwitch
          v-if="themeStore.header.multilingual.visible"
          :lang="appStore.locale"
          :lang-options="appStore.localeOptions"
          :show-tooltip="false"
          @change-lang="appStore.changeLocale"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.login-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, rgb(var(--layout-bg-color)) 0%, rgb(var(--layout-bg-color)) 100%);
}

.login-container {
  width: 380px;
  padding: 48px 40px;
  background: rgb(var(--container-bg-color));
  border-radius: 16px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.04);
}

.login-header {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  margin-bottom: 32px;
}

.login-logo {
  width: 48px;
  height: 48px;
}

.login-title {
  font-size: 24px;
  font-weight: 600;
  color: rgb(var(--base-text-color));
  margin: 0;
}

.login-tabs {
  display: flex;
  gap: 4px;
  padding: 4px;
  background: rgba(var(--inverted-bg-color), 0.1);
  border-radius: 8px;
  margin-bottom: 28px;
}

.login-tab {
  flex: 1;
  padding: 10px 16px;
  border: none;
  background: transparent;
  border-radius: 6px;
  font-size: 14px;
  color: rgb(var(--base-text-color));
  opacity: 0.6;
  cursor: pointer;
  transition: all 0.2s ease;
}

.login-tab:hover {
  opacity: 0.8;
}

.login-tab.active {
  background: rgb(var(--container-bg-color));
  color: rgb(var(--base-text-color));
  opacity: 1;
  font-weight: 500;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.login-form {
  min-height: 200px;
}

.login-footer {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 32px;
  padding-top: 24px;
  border-top: 1px solid rgba(var(--inverted-bg-color), 0.1);
}
</style>
