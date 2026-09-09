<template>
  <div class="profile-auth">
    <div class="profile-auth-main">
      <el-table :data="auths" border class="data-table profile-auth-table" show-overflow-tooltip>
        <el-table-column label="序号" width="50" type="index" />
        <el-table-column label="绑定账号平台" min-width="120" align="center" prop="source" show-overflow-tooltip />
        <el-table-column label="头像" width="90" align="center" prop="avatar">
          <template #default="scope">
            <img :src="scope.row.avatar" class="auth-avatar" />
          </template>
        </el-table-column>
        <el-table-column label="系统账号" min-width="140" align="center" prop="userName" :show-overflow-tooltip="true" />
        <el-table-column label="绑定时间" min-width="160" align="center" prop="createTime" />
        <el-table-column label="操作" width="80" align="center" class-name="small-padding fixed-width">
          <template #default="scope">
            <el-tooltip content="解绑" placement="top">
              <el-button link type="primary" icon="CircleClose" @click="unlockAuth(scope.row)"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div class="provider-section">
      <div class="provider-heading">
        <h4 class="provider-desc">可绑定的第三方应用</h4>
        <p>点击右侧平台完成账号绑定。</p>
      </div>
      <div class="user-bind">
        <a class="third-app" href="#" title="使用 微信 账号授权登录" @click="authUrl('wechat')">
          <div class="third-app__icon">
            <svg-icon icon-class="wechat" />
          </div>
          <span class="app-name">微信</span>
        </a>
        <a class="third-app" href="#" title="使用 MaxKey 账号授权登录" @click="authUrl('maxkey')">
          <div class="third-app__icon">
            <svg-icon icon-class="maxkey" />
          </div>
          <span class="app-name">MaxKey</span>
        </a>
        <a class="third-app" href="#" title="使用 TopIam 账号授权登录" @click="authUrl('topiam')">
          <div class="third-app__icon">
            <svg-icon icon-class="topiam" />
          </div>
          <span class="app-name">TopIam</span>
        </a>
        <a class="third-app" href="#" title="使用 Gitee 账号授权登录" @click="authUrl('gitee')">
          <div class="third-app__icon">
            <svg-icon icon-class="gitee" />
          </div>
          <span class="app-name">Gitee</span>
        </a>
        <a class="third-app" href="#" title="使用 GitHub 账号授权登录" @click="authUrl('github')">
          <div class="third-app__icon">
            <svg-icon icon-class="github" />
          </div>
          <span class="app-name">GitHub</span>
        </a>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { authUnlock, authRouterUrl } from '@/api/system/social/auth';
import modal from '@/plugins/modal';
import tab from '@/plugins/tab';
import { propTypes } from '@/utils/propTypes';

const props = defineProps({
  auths: propTypes.any.isRequired
});
const auths = computed(() => props.auths);

const unlockAuth = (row: any) => {
  ElMessageBox.confirm('您确定要解除"' + row.source + '"的账号绑定吗？')
    .then(() => {
      return authUnlock(row.id);
    })
    .then((res: any) => {
      if (res.code === 200) {
        modal.msgSuccess('解绑成功');
        tab.refreshPage();
      } else {
        modal.msgError(res.msg);
      }
    })
    .catch(() => {});
};

const authUrl = (source: string) => {
  authRouterUrl(source).then((res: any) => {
    if (res.code === 200) {
      window.location.href = res.data;
    } else {
      modal.msgError(res.msg);
    }
  });
};
</script>

<style lang="scss" scoped>
.profile-auth {
  display: flex;
  align-items: stretch;
  gap: 16px;
}

.profile-auth-main {
  flex: 1 1 auto;
  min-width: 0;
}

.profile-auth-table {
  width: 100%;
}

.auth-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  object-fit: cover;
  vertical-align: middle;
}

.provider-section {
  flex: 0 0 168px;
  display: flex;
  flex-direction: column;
  gap: 14px;
  padding-left: 16px;
  border-left: 1px solid var(--app-surface-border);
}

.provider-heading p {
  margin: 6px 0 0;
  color: var(--el-text-color-secondary);
  font-size: 12px;
  line-height: 1.5;
}

.user-bind {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.user-bind .third-app {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-start;
  gap: 10px;
  min-height: 44px;
  padding: 8px 4px;
  border: none;
  border-radius: 0;
  background: transparent;
  transition: background-color 0.2s ease;
}

.third-app:hover {
  background: var(--app-accent-soft);
}

.third-app__icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 0;
  background: rgba(53, 109, 255, 0.08);
  font-size: 18px;
  flex-shrink: 0;
}

a {
  text-decoration: none;
  cursor: pointer;
  color: inherit;
}

.provider-desc {
  margin: 0;
  font-size: 15px;
}

.app-name {
  font-weight: 600;
  font-size: 13px;
  line-height: 1.2;
  text-align: left;
  word-break: break-word;
}

@media (max-width: 900px) {
  .profile-auth {
    flex-direction: column;
  }

  .provider-section {
    flex: none;
    width: 100%;
    padding-left: 0;
    padding-top: 12px;
    border-left: none;
    border-top: 1px solid var(--app-surface-border);
  }

  .user-bind {
    flex-direction: row;
    flex-wrap: wrap;
  }

  .user-bind .third-app {
    flex-direction: column;
    min-width: 72px;
    min-height: 72px;
    justify-content: center;
  }

  .app-name {
    text-align: center;
  }
}

html.dark {
  .third-app__icon {
    background: rgba(96, 165, 250, 0.12);
  }
}
</style>
