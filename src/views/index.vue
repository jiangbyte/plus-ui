<template>
  <div class="home">
    <section class="hero-panel">
      <div class="hero-copy">
        <h1>RuoYi-Vue-Plus 控制台</h1>
        <p>
          企业级后台管理系统 重写 RuoYi-Vue 所有功能 集成 Sa-Token、Mybatis-Plus、WarmFlow、SpringDoc、Hutool、OSS
          等组件
        </p>
        <div class="hero-actions">
          <el-button type="primary" @click="goTarget('https://gitee.com/dromara/RuoYi-Vue-Plus')">查看源码</el-button>
          <el-button plain @click="goTarget('https://plus-doc.dromara.org/#/ruoyi-vue-plus/changlog')">
            更新日志
          </el-button>
        </div>
      </div>
    </section>

    <div class="content-grid">
      <section class="section-card">
        <div class="section-head">
          <div>
            <h2>项目矩阵</h2>
          </div>
        </div>
        <div class="product-list">
          <article v-for="product in products" :key="product.name" class="product-card">
            <div class="product-top">
              <div>
                <h3>{{ product.name }}</h3>
                <p>{{ product.summary }}</p>
              </div>
              <span class="product-version">{{ product.version }}</span>
            </div>
            <div class="product-tags">
              <el-tag v-for="tag in product.tags" :key="tag" effect="plain">{{ tag }}</el-tag>
            </div>
            <div class="product-actions">
              <el-button type="primary" plain @click="goTarget(product.primaryUrl)">
                {{ product.primaryLabel }}
              </el-button>
              <el-button plain @click="goTarget(product.secondaryUrl)">{{ product.secondaryLabel }}</el-button>
            </div>
          </article>
        </div>
      </section>

      <section class="section-card capability-card">
        <div class="section-head">
          <div>
            <h2>能力地图</h2>
          </div>
        </div>
        <div class="capability-groups">
          <article v-for="group in capabilityGroups" :key="group.title" class="capability-group">
            <h3>{{ group.title }}</h3>
            <ul>
              <li v-for="item in group.items" :key="item">{{ item }}</li>
            </ul>
          </article>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup name="Index" lang="ts">
const products = [
  {
    name: 'RuoYi-Vue-Plus',
    version: 'v6.0.0',
    summary: '面向分布式集群场景的后台管理系统，保持现有业务接口与权限逻辑，适合先完成前端壳升级。',
    tags: ['Vue 3', 'Element Plus', 'Spring Boot', 'Sa-Token'],
    primaryLabel: '访问 GitHub',
    primaryUrl: 'https://github.com/dromara/RuoYi-Vue-Plus',
    secondaryLabel: '查看更新日志',
    secondaryUrl: 'https://plus-doc.dromara.org/#/ruoyi-vue-plus/changlog'
  },
  {
    name: 'RuoYi-Cloud-Plus',
    version: 'v6.0.0',
    summary: '微服务通用权限管理系统，适合更复杂的服务治理场景，也可以沿用同样的前端升级思路。',
    tags: ['Spring Cloud', 'Gateway', 'Nacos', 'Dubbo'],
    primaryLabel: '访问 GitHub',
    primaryUrl: 'https://github.com/dromara/RuoYi-Cloud-Plus',
    secondaryLabel: '查看更新日志',
    secondaryUrl: 'https://plus-doc.dromara.org/#/ruoyi-cloud-plus/changlog'
  }
];

const capabilityGroups = [
  {
    title: '后端基建',
    items: ['Spring Boot / Spring Cloud', 'Sa-Token 认证与权限', 'MySQL / Redis', '代码生成器']
  },
  {
    title: '平台能力',
    items: ['动态菜单与按钮权限', '监控、日志、在线用户', '任务调度与工作流', '文件存储与多云适配']
  },
  {
    title: '前端方向',
    items: ['UI 卡片化', '主题与布局统一', '通用页面容器规范化', '企业化布局']
  }
];

const goTarget = (url: string) => {
  window.open(url, '__blank');
};
</script>

<style lang="scss" scoped>
.home {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.hero-panel,
.section-card {
  border-radius: 0;
  border: 1px solid var(--app-surface-border);
  background: var(--app-surface-bg);
  box-shadow: none;
  backdrop-filter: none;
}

.hero-panel {
  display: grid;
  grid-template-columns: minmax(0, 1.3fr) minmax(280px, 0.7fr);
  gap: 12px;
  padding: 16px 18px;
  background: var(--app-surface-bg);
}

.hero-copy {
  display: flex;
  flex-direction: column;
  gap: 8px;

  h1 {
    margin: 0;
    font-size: clamp(24px, 3.2vw, 34px);
    line-height: 1.15;
    letter-spacing: -0.03em;
    color: var(--app-text-title);
  }

  p {
    margin: 0;
    max-width: 760px;
    color: var(--app-text-muted);
    font-size: 14px;
    line-height: 1.6;
  }
}

.hero-badge {
  display: inline-flex;
  width: fit-content;
  padding: 4px 10px;
  border-radius: 0;
  background: rgba(53, 109, 255, 0.12);
  color: var(--app-accent-strong);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 2px;
}

.hero-stats {
  display: grid;
  gap: 8px;
}

.stat-card {
  padding: 12px 14px;
  border-radius: 0;
  background: var(--app-elevated-soft-bg);
  border: 1px solid var(--app-surface-border);
  display: flex;
  flex-direction: column;
  gap: 4px;

  strong {
    color: var(--app-text-title);
    font-size: 20px;
    letter-spacing: -0.03em;
  }

  span {
    color: var(--app-text-muted);
    font-size: 12px;
  }
}

.content-grid {
  display: grid;
  grid-template-columns: minmax(0, 1.15fr) minmax(300px, 0.85fr);
  gap: 8px;
}

.section-card {
  padding: 14px 16px;
}

.section-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;

  h2 {
    margin: 0;
    color: var(--app-text-title);
    font-size: 18px;
    letter-spacing: -0.02em;
  }
}

.section-kicker {
  color: var(--app-text-muted);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.product-list,
.capability-groups {
  display: grid;
  gap: 8px;
}

.product-card {
  padding: 12px 14px;
  border-radius: 0;
  background: var(--app-elevated-soft-bg);
  border: 1px solid var(--app-surface-border);
  transition: border-color 0.2s ease;

  &:hover {
    transform: none;
    box-shadow: none;
    border-color: rgba(53, 109, 255, 0.2);
  }
}

.product-top {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;

  h3 {
    margin: 0 0 4px;
    color: var(--app-text-title);
    font-size: 16px;
    letter-spacing: -0.02em;
  }

  p {
    margin: 0;
    color: var(--app-text-muted);
    line-height: 1.55;
    font-size: 13px;
  }
}

.product-version {
  flex-shrink: 0;
  padding: 4px 8px;
  border-radius: 0;
  background: rgba(53, 109, 255, 0.12);
  color: var(--app-accent-strong);
  font-size: 12px;
  font-weight: 700;
}

.product-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin-top: 10px;
}

.product-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 10px;
}

.capability-card {
  background: var(--app-surface-bg);
}

.capability-group {
  padding: 12px 14px;
  border-radius: 0;
  background: var(--app-elevated-soft-bg);
  border: 1px solid var(--app-surface-border);

  h3 {
    margin: 0 0 8px;
    color: var(--app-text-title);
    font-size: 15px;
  }

  ul {
    margin: 0;
    padding: 0;
    list-style: none;
    display: grid;
    gap: 6px;
  }

  li {
    position: relative;
    padding-left: 14px;
    color: var(--app-text-muted);
    line-height: 1.5;
    font-size: 13px;

    &::before {
      content: '';
      position: absolute;
      left: 0;
      top: 7px;
      width: 6px;
      height: 6px;
      border-radius: 50%;
      background: var(--app-accent-strong);
      box-shadow: none;
    }
  }
}

@media (max-width: 960px) {
  .hero-panel,
  .content-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 640px) {
  .hero-panel,
  .section-card {
    padding: 12px;
    border-radius: 0;
  }

  .product-top {
    flex-direction: column;
  }
}

html.dark {
  .hero-panel {
    background: var(--app-surface-bg);
  }
}
</style>
