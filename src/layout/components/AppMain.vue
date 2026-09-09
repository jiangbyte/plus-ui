<template>
  <section class="app-main">
    <router-view v-slot="{ Component, route }">
      <transition :enter-active-class="animate" mode="out-in">
        <keep-alive :include="tagsViewStore.cachedViews">
          <component :is="Component" v-if="!route.meta.link" :key="route.path" />
        </keep-alive>
      </transition>
    </router-view>
    <iframe-toggle />
  </section>
</template>

<script setup name="AppMain" lang="ts">
import animateConfig from '@/animate';
import { useFullHeightTable } from '@/hooks/table/useFullHeightTable';
import { useSettingsStore } from '@/store/modules/settings';
import { useTagsViewStore } from '@/store/modules/tagsView';
import IframeToggle from './IframeToggle/index.vue';

const route = useRoute();
const tagsViewStore = useTagsViewStore();
useFullHeightTable();

// 随机动画集合
const animate = ref<string>('');
watch(
  () => useSettingsStore().animationEnable,
  (val: boolean) => {
    if (val) {
      animate.value = animateConfig.animateList[Math.floor(Math.random() * animateConfig.animateList.length)] as string;
    } else {
      animate.value = animateConfig.defaultAnimate as string;
    }
  },
  { immediate: true }
);

watchEffect(() => {
  addIframe();
});

function addIframe() {
  if (route.meta.link) {
    useTagsViewStore().addIframeView(route);
  }
}
</script>

<style lang="scss" scoped>
.app-main {
  width: 100%;
  position: relative;
  overflow-x: hidden;
  overflow-y: auto;
  padding: 0;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  height: 100vh;
  min-height: 100vh;

  &:fullscreen,
  &:-webkit-full-screen,
  &:-moz-full-screen,
  &:-ms-fullscreen {
    background: var(--el-bg-color);
    overflow-y: auto;
  }
}

.app-main:not(.with-fixed-header) {
  height: calc(100vh - 52px);
  min-height: calc(100vh - 52px);
}

.app-main.with-tags-view:not(.with-fixed-header) {
  height: calc(100vh - 90px);
  min-height: calc(100vh - 90px);
}

.app-main.with-fixed-header {
  padding-top: 52px;
  height: 100vh;
  min-height: 100vh;
}

.app-main.with-fixed-header.with-tags-view {
  padding-top: 90px;
  height: 100vh;
  min-height: 100vh;
}

/* 首页等非全高页：内容超出时可滚动，不被 flex 压缩 */
.app-main > :not(.p-2) {
  flex-shrink: 0;
}

@media (max-width: 900px) {
  .app-main,
  .app-main:not(.with-fixed-header),
  .app-main.with-tags-view:not(.with-fixed-header),
  .app-main.with-fixed-header,
  .app-main.with-fixed-header.with-tags-view {
    height: auto;
    min-height: 100%;
    overflow: visible;
    overflow-y: auto;
  }
}
</style>
<style lang="scss">
// fix css style bug in open el-dialog
.el-popup-parent--hidden {
  .fixed-header {
    padding-right: 6px;
  }
}

::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

::-webkit-scrollbar-track {
  background-color: var(--el-fill-color-lighter);
}

::-webkit-scrollbar-thumb {
  background-color: var(--el-text-color-placeholder);
  border-radius: 0;
}
</style>
