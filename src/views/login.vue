<template>
  <div class="login">
    <div class="login-logo">{{ title }}</div>

    <div class="login-shell">
      <section class="login-brand">
        <div class="brand-copy">
          <h1 class="brand-title">企业级后台管理系统</h1>
          <p class="brand-desc">
            真正面向企业级的应用框架，组件化、模块化、轻耦合、高扩展，集成 Sa-Token、Mybatis-Plus、WarmFlow 等主流技术栈。
          </p>
        </div>
      </section>

      <el-form ref="loginRef" :model="loginForm" :rules="loginRules" class="login-form">
        <div class="title-box">
          <h3 class="title">欢迎登录</h3>
          <p class="subtitle">{{ title }}</p>
          <lang-select class="lang-switch" />
        </div>

        <el-form-item prop="username">
          <el-input
            v-model="loginForm.username"
            type="text"
            size="large"
            auto-complete="off"
            :placeholder="$t('login.username')"
          >
            <template #prefix><svg-icon icon-class="user" class="el-input__icon input-icon" /></template>
          </el-input>
        </el-form-item>

        <el-form-item prop="password">
          <el-input
            v-model="loginForm.password"
            type="password"
            size="large"
            auto-complete="off"
            :placeholder="$t('login.password')"
            @keyup.enter="handleLogin"
          >
            <template #prefix><svg-icon icon-class="password" class="el-input__icon input-icon" /></template>
          </el-input>
        </el-form-item>

        <el-form-item v-if="captchaEnabled" prop="code" class="captcha-row">
          <el-input
            v-model="loginForm.code"
            size="large"
            auto-complete="off"
            :placeholder="$t('login.code')"
            @keyup.enter="handleLogin"
          >
            <template #prefix><svg-icon icon-class="validCode" class="el-input__icon input-icon" /></template>
          </el-input>
          <div class="login-code">
            <img :src="codeUrl" class="login-code-img" @click="getCode" />
          </div>
        </el-form-item>

        <div class="form-meta">
          <router-link v-if="register" class="link-type" :to="'/register'">
            {{ $t('login.switchRegisterPage') }}
          </router-link>
          <span v-else></span>
          <el-checkbox v-model="loginForm.rememberMe">{{ $t('login.rememberPassword') }}</el-checkbox>
        </div>

        <div class="social-panel">
          <span class="social-label">第三方登录</span>
          <div class="social-actions">
            <el-button circle :title="$t('login.social.wechat')" @click="doSocialLogin('wechat')">
              <svg-icon icon-class="wechat" />
            </el-button>
            <el-button circle :title="$t('login.social.maxkey')" @click="doSocialLogin('maxkey')">
              <svg-icon icon-class="maxkey" />
            </el-button>
            <el-button circle :title="$t('login.social.topiam')" @click="doSocialLogin('topiam')">
              <svg-icon icon-class="topiam" />
            </el-button>
            <el-button circle :title="$t('login.social.gitee')" @click="doSocialLogin('gitee')">
              <svg-icon icon-class="gitee" />
            </el-button>
            <el-button circle :title="$t('login.social.github')" @click="doSocialLogin('github')">
              <svg-icon icon-class="github" />
            </el-button>
          </div>
        </div>

        <el-form-item class="submit-row">
          <el-button :loading="loading" size="large" type="primary" class="submit-button" @click.prevent="handleLogin">
            <span v-if="!loading">{{ $t('login.login') }}</span>
            <span v-else>{{ $t('login.logging') }}</span>
          </el-button>
        </el-form-item>
      </el-form>
    </div>

    <div class="el-login-footer">
      <span>Copyright © 2018-{{ currentYear }} 疯狂的狮子Li All Rights Reserved.</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { to } from 'await-to-js';
import { useI18n } from 'vue-i18n';
import { getCodeImg } from '@/api/login';
import { authRouterUrl } from '@/api/system/social/auth';
import { LoginData } from '@/api/types';
import { HttpStatus } from '@/enums/RespEnum';
import { useUserStore } from '@/store/modules/user';

const title = import.meta.env.VITE_APP_TITLE;
const currentYear = new Date().getFullYear();
const userStore = useUserStore();
const router = useRouter();
const { t } = useI18n();

const loginForm = ref<LoginData>({
  username: 'admin',
  password: 'admin123',
  rememberMe: false,
  code: '',
  uuid: ''
} as LoginData);

const loginRules: ElFormRules = {
  username: [
    {
      required: true,
      trigger: 'blur',
      message: t('login.rule.username.required')
    }
  ],
  password: [
    {
      required: true,
      trigger: 'blur',
      message: t('login.rule.password.required')
    }
  ],
  code: [
    {
      required: true,
      trigger: 'change',
      message: t('login.rule.code.required')
    }
  ]
};

const codeUrl = ref('');
const loading = ref(false);
const captchaEnabled = ref(true);
const register = ref(false);
const redirect = ref('/');
const loginRef = ref<ElFormInstance>();

watch(
  () => router.currentRoute.value,
  (newRoute: any) => {
    redirect.value = newRoute.query && newRoute.query.redirect && decodeURIComponent(newRoute.query.redirect);
  },
  { immediate: true }
);

const handleLogin = () => {
  loginRef.value?.validate(async (valid: boolean, fields: any) => {
    if (valid) {
      loading.value = true;
      if (loginForm.value.rememberMe) {
        localStorage.setItem('username', String(loginForm.value.username));
        localStorage.setItem('rememberMe', String(loginForm.value.rememberMe));
      } else {
        localStorage.removeItem('username');
        localStorage.removeItem('rememberMe');
      }
      localStorage.removeItem('password');
      const [err] = await to(userStore.login(loginForm.value));
      if (!err) {
        const redirectUrl = redirect.value || '/';
        await router.push(redirectUrl);
        loading.value = false;
      } else {
        loading.value = false;
        if (captchaEnabled.value) {
          await getCode();
        }
      }
    } else {
      console.log('error submit!', fields);
    }
  });
};

const getCode = async () => {
  const res = await getCodeImg();
  const { data } = res;
  captchaEnabled.value = data.captchaEnabled === undefined ? true : data.captchaEnabled;
  if (captchaEnabled.value) {
    loginForm.value.code = '';
    codeUrl.value = 'data:image/gif;base64,' + data.img;
    loginForm.value.uuid = data.uuid;
  }
};

const getLoginData = () => {
  const username = localStorage.getItem('username');
  const rememberMe = localStorage.getItem('rememberMe');
  localStorage.removeItem('password');
  loginForm.value = {
    username: username === null ? String(loginForm.value.username) : username,
    password: username === null ? String(loginForm.value.password) : '',
    rememberMe: rememberMe === 'true'
  } as LoginData;
};

const doSocialLogin = (type: string) => {
  authRouterUrl(type).then((res: any) => {
    if (res.code === HttpStatus.SUCCESS) {
      window.location.href = res.data;
    } else {
      ElMessage.error(res.msg);
    }
  });
};

onMounted(() => {
  getCode();
  getLoginData();
});
</script>

<style lang="scss" scoped>
.login {
  position: relative;
  min-height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 56px 24px 72px;
  background: #eef4fb;
}

.login-logo {
  position: absolute;
  top: 20px;
  left: 28px;
  color: var(--app-accent-strong);
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 0.02em;
}

.login-shell {
  width: min(1080px, 100%);
  min-height: 560px;
  display: grid;
  grid-template-columns: minmax(0, 1.25fr) minmax(340px, 0.9fr);
  overflow: hidden;
  border: 1px solid var(--app-surface-border);
  background: var(--app-surface-bg);
}

.login-brand {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 48px 40px;
  color: #fff;
  background: #0f5fad;
}

.brand-copy {
  max-width: 420px;
  text-align: center;
}

.brand-title {
  margin: 0 0 14px;
  font-size: clamp(28px, 3.2vw, 40px);
  line-height: 1.2;
  letter-spacing: -0.02em;
  font-weight: 700;
}

.brand-desc {
  margin: 0;
  color: rgba(255, 255, 255, 0.88);
  font-size: 14px;
  line-height: 1.7;
}

.login-form {
  position: relative;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 42px 40px 36px;
  background: #fff;
}

.title-box {
  position: relative;
  margin-bottom: 28px;
  text-align: center;

  .title {
    margin: 0;
    color: var(--app-accent-strong);
    font-weight: 700;
    font-size: 28px;
    letter-spacing: -0.02em;
  }

  .subtitle {
    margin: 8px 0 0;
    color: var(--app-text-muted);
    font-size: 13px;
    line-height: 1.5;
  }

  .lang-switch {
    position: absolute;
    top: 0;
    right: 0;
  }

  :deep(.lang-select--style) {
    line-height: 0;
    color: var(--app-text-muted);
    padding: 8px;
    border: none;
    background: transparent;
  }
}

.login-form .el-input {
  height: 44px;
}

.login-form .input-icon {
  height: 16px;
  width: 14px;
  margin-left: 0;
}

.login-form :deep(.el-input__wrapper) {
  height: 44px;
  min-height: 44px;
  max-height: 44px;
  padding-top: 0;
  padding-bottom: 0;
  background-color: transparent;
  border-radius: 0;
  box-shadow: 0 0 0 1px var(--app-surface-border) inset;
  box-sizing: border-box;
}

.login-form :deep(.el-input__inner) {
  height: 44px;
  line-height: 44px;
  font-size: 14px;
}

/* 浏览器密码框默认字体会撑高输入框，强制与账号框一致 */
.login-form :deep(input[type='password']) {
  font-family: inherit;
  font-size: 14px;
  line-height: 44px;
  letter-spacing: 0.12em;
}

.captcha-row {
  :deep(.el-form-item__content) {
    display: grid;
    grid-template-columns: minmax(0, 1fr) 122px;
    gap: 12px;
  }
}

.form-meta {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin: -2px 0 16px;
}

.social-panel {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 18px;
}

.social-label {
  color: var(--app-text-muted);
  font-size: 13px;
  font-weight: 600;
  white-space: nowrap;
}

.social-actions {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-end;
  gap: 8px;
}

.submit-row {
  margin-bottom: 0;
}

.submit-button {
  width: 100%;
  height: 46px;
  border-radius: 0;
  box-shadow: none;
}

.login-form :deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 0 0 1px var(--app-accent-strong) inset;
}

.login-form :deep(.el-checkbox__label) {
  color: var(--app-text-muted);
}

.login-form :deep(.el-button.is-circle) {
  background: transparent;
  border: none;
  color: var(--app-text-muted);
  box-shadow: none;
}

.login-form :deep(.el-button.is-circle:hover) {
  background: transparent;
  border: none;
  color: var(--app-accent-strong);
}

.login-code {
  height: 44px;
  box-sizing: border-box;
  overflow: hidden;
  background: var(--el-bg-color);
  border: 1px solid var(--app-surface-border);

  img {
    cursor: pointer;
    vertical-align: middle;
    display: block;
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}

.el-login-footer {
  height: 40px;
  line-height: 40px;
  position: fixed;
  bottom: 0;
  width: 100%;
  text-align: center;
  color: var(--app-text-muted);
  font-size: 12px;
  letter-spacing: 0.04em;
}

.login-code-img {
  height: 44px;
  padding-left: 0;
}

.link-type {
  color: var(--app-accent-strong);
  font-size: 13px;
}

@media (max-width: 960px) {
  .login {
    padding: 64px 14px 72px;
  }

  .login-shell {
    grid-template-columns: 1fr;
    min-height: auto;
  }

  .login-brand {
    min-height: 180px;
    padding: 28px 24px;
  }

  .login-form {
    padding: 28px 24px 24px;
  }
}

@media (max-width: 640px) {
  .login-logo {
    left: 16px;
    top: 16px;
    font-size: 16px;
  }

  .login-brand {
    display: none;
  }

  .login-form {
    padding: 24px 18px 20px;
  }

  .title-box .lang-switch {
    position: static;
    display: flex;
    justify-content: flex-end;
    margin-bottom: 8px;
  }

  .social-panel {
    flex-direction: column;
    align-items: flex-start;
  }

  .social-actions {
    justify-content: flex-start;
  }

  .captcha-row :deep(.el-form-item__content) {
    grid-template-columns: 1fr;
  }
}
</style>
