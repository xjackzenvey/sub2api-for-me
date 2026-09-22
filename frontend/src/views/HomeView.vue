<template>
  <!-- Custom Home Content: keep the admin-configured surface fully intact. -->
  <div v-if="hasHomeContent" class="min-h-screen">
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <div v-else v-html="homeContent"></div>
  </div>

  <!-- Compact Home Page -->
  <div v-else-if="compactHomeEnabled" data-testid="compact-home" class="ciallo-compact">
    <div class="ciallo-compact__glow" aria-hidden="true"></div>
    <header class="ciallo-compact__nav">
      <div class="ciallo-brand">
        <span class="ciallo-brand__mark">
          <img :src="siteLogo || '/assets/soulai-logo.png'" alt="SoulAI logo" />
        </span>
        <span>
          <span class="ciallo-brand__name">{{ displaySiteName }}</span>
          <span class="ciallo-brand__tag">AI API gateway</span>
        </span>
      </div>

      <div class="ciallo-compact__actions">
        <LocaleSwitcher />
        <a
          v-if="docUrl"
          :href="docUrl"
          target="_blank"
          rel="noopener noreferrer"
          class="ciallo-navlink"
          :title="t('home.viewDocs')"
        >
          <Icon name="book" size="sm" />
          <span>{{ t('nav.docs') }}</span>
        </a>
        <router-link
          v-if="showModelPlazaEntry"
          to="/model-plaza"
          class="ciallo-navlink"
        >
          <Icon name="grid" size="sm" />
          <span>{{ t('nav.modelPlaza') }}</span>
        </router-link>
        <button class="ciallo-navlink" type="button" @click="toggleTheme">
          <Icon v-if="isDark" name="sun" size="sm" />
          <Icon v-else name="moon" size="sm" />
        </button>
        <router-link
          :to="entryPath"
          class="ciallo-navlink ciallo-navlink--accent"
        >
          {{ isAuthenticated ? t('home.dashboard') : t('home.login') }}
        </router-link>
      </div>
    </header>

    <main class="ciallo-compact__main">
      <section class="ciallo-compact__panel">
        <img :src="siteLogo || '/assets/soulai-logo.png'" alt="SoulAI logo" />
        <h1>{{ displaySiteName }}</h1>
        <p>{{ siteSubtitle }}</p>
        <router-link :to="entryPath" class="ciallo-landing__button">
          {{ isAuthenticated ? t('home.goToDashboard') : t('home.login') }}
          <Icon name="arrowRight" size="sm" />
        </router-link>
      </section>
    </main>

    <footer class="ciallo-compact__footer">
      <span>© {{ currentYear }} {{ displaySiteName }} · route with a little magic</span>
    </footer>
  </div>

  <!-- SoulAI Default Home -->
  <div v-else class="ciallo-landing">
    <div class="ciallo-landing__glow" aria-hidden="true"></div>
    <div class="ciallo-landing__stars" aria-hidden="true">
      <span
        v-for="(star, index) in starField"
        :key="index"
        class="ciallo-star"
        :style="star"
      ></span>
    </div>

    <header class="ciallo-landing__nav">
      <div class="ciallo-brand">
        <span class="ciallo-brand__mark">
          <img :src="siteLogo || '/assets/soulai-logo.png'" alt="SoulAI logo" />
        </span>
        <span>
          <span class="ciallo-brand__name">{{ displaySiteName }}</span>
          <span class="ciallo-brand__tag">AI API gateway</span>
        </span>
      </div>

      <nav class="ciallo-navlinks" aria-label="Primary navigation">
        <a
          v-if="docUrl"
          :href="docUrl"
          target="_blank"
          rel="noopener noreferrer"
          class="ciallo-navlink"
        >
          <Icon name="book" size="sm" />
          <span>{{ t('nav.docs') }}</span>
        </a>
        <router-link
          v-if="showModelPlazaEntry"
          to="/model-plaza"
          class="ciallo-navlink"
        >
          <Icon name="grid" size="sm" />
          <span>{{ t('nav.modelPlaza') }}</span>
        </router-link>
        <LocaleSwitcher />
        <button class="ciallo-navlink" type="button" @click="toggleTheme">
          <Icon v-if="isDark" name="sun" size="sm" />
          <Icon v-else name="moon" size="sm" />
        </button>
        <router-link :to="entryPath" class="ciallo-navlink ciallo-navlink--accent">
          {{ isAuthenticated ? t('home.dashboard') : t('home.login') }}
        </router-link>
      </nav>
    </header>

    <main class="ciallo-landing__hero">
      <section class="ciallo-landing__copy">
        <div class="ciallo-signal">one gateway · every model</div>
        <h1>
          让请求
          <span>抵达合适的</span>
          <em>模型。</em>
        </h1>
        <p class="ciallo-landing__lede">
          {{ displaySiteName }} 是一个专注于请求本身的 AI API 网关：统一密钥、清晰路由、透明用量。
        </p>
        <div class="ciallo-landing__actions">
          <router-link :to="entryPath" class="ciallo-landing__button">
            {{ isAuthenticated ? t('home.goToDashboard') : '开始连接' }}
            <Icon name="arrowRight" size="sm" />
          </router-link>
          <router-link
            v-if="showModelPlazaEntry"
            to="/model-plaza"
            class="ciallo-landing__button ciallo-landing__button--ghost"
          >
            探索模型广场
          </router-link>
          <a
            v-else-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="ciallo-landing__button ciallo-landing__button--ghost"
          >
            阅读接入指南
          </a>
        </div>

        <div class="ciallo-landing__facts" aria-label="SoulAI highlights">
          <div class="ciallo-fact">
            <strong>01</strong>
            <span>一把 Key，接入多模型</span>
          </div>
          <div class="ciallo-fact">
            <strong>24/7</strong>
            <span>稳定的请求转发</span>
          </div>
          <div class="ciallo-fact">
            <strong>∞</strong>
            <span>清晰可追踪的用量</span>
          </div>
        </div>
      </section>

      <section class="ciallo-landing__visual" aria-label="SoulAI route monitor">
        <div class="ciallo-orbit" aria-hidden="true">
          <span class="ciallo-orbit__spark"></span>
        </div>
        <div class="ciallo-avatar" aria-hidden="true">✦</div>
        <div class="ciallo-terminal terminal-container">
          <div class="ciallo-terminal__bar">
            <span class="ciallo-terminal__dots" aria-hidden="true"><i></i><i></i><i></i></span>
            <span>soulai / route-monitor</span>
            <span>LIVE</span>
          </div>
          <div class="ciallo-terminal__body">
            <div class="ciallo-terminal__line"><strong>signal</strong><span>finding a clear path for <span class="pink">claude-3.7</span></span></div>
            <div class="ciallo-terminal__line"><strong>route</strong><span><em>✓ active</em> · balanced lane</span></div>
            <div class="ciallo-terminal__line"><strong>relay</strong><span>https://api.soulai.ai/v1</span></div>
            <div class="ciallo-terminal__line"><strong>latency</strong><span><span class="yellow">620ms</span> · 99.98% uptime</span></div>
            <div class="ciallo-terminal__line"><strong>mode</strong><span>quiet power / <span class="pink">high focus</span></span></div>
            <div class="ciallo-terminal__meter"><span></span><b>82%</b></div>
          </div>
          <div class="ciallo-terminal__badge">upstream synced</div>
        </div>
      </section>
    </main>

    <section class="ciallo-feature-rail" aria-label="SoulAI features">
      <article class="ciallo-feature-card">
        <div class="ciallo-feature-card__icon"><Icon name="link" size="sm" /></div>
        <strong>一个入口</strong>
        <p>OpenAI、Claude、Gemini 等模型，使用同一套调用习惯。</p>
      </article>
      <article class="ciallo-feature-card">
        <div class="ciallo-feature-card__icon"><Icon name="sync" size="sm" /></div>
        <strong>聪明路由</strong>
        <p>把请求交给最合适的上游，少一点等待，多一点专注。</p>
      </article>
      <article class="ciallo-feature-card">
        <div class="ciallo-feature-card__icon"><Icon name="chart" size="sm" /></div>
        <strong>可见用量</strong>
        <p>每一笔请求、Token 与成本，都在你的控制台里清清楚楚。</p>
      </article>
    </section>

    <footer class="ciallo-landing__footer">
      <span><strong>{{ displaySiteName }}</strong> · built for curious builders</span>
      <span>© {{ currentYear }} · clarity for every request</span>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'
import { sanitizeUrl } from '@/utils/url'
import { FeatureFlags, isFeatureFlagEnabled } from '@/utils/featureFlags'

const { t } = useI18n()

const authStore = useAuthStore()
const appStore = useAppStore()

// Keep custom admin-configured names, but replace the upstream default in the
// Keep the public brand stable without rewriting administrator-provided custom copy.
const rawSiteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'SoulAI')
const displaySiteName = computed(() => rawSiteName.value === 'Sub2API' || rawSiteName.value === 'CialloAI' ? 'SoulAI' : rawSiteName.value)
const siteLogo = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || '一站式 AI API 中转与模型路由，让每一次请求都顺滑抵达。')
const docUrl = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.doc_url || appStore.docUrl || ''))
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')
const hasHomeContent = computed(() => homeContent.value.trim().length > 0)
const compactHomeEnabled = computed(() => appStore.cachedPublicSettings?.compact_home_enabled === true)
const modelPlazaEnabled = computed(() => isFeatureFlagEnabled(FeatureFlags.modelPlaza))

const isHomeContentUrl = computed(() => {
  const content = homeContent.value.trim()
  return content.startsWith('http://') || content.startsWith('https://')
})

const isDark = ref(document.documentElement.classList.contains('dark'))
const isAuthenticated = computed(() => authStore.isAuthenticated)
const modelPlazaRequiresAuth = computed(() => appStore.cachedPublicSettings?.model_plaza_require_auth === true)
const showModelPlazaEntry = computed(() => modelPlazaEnabled.value && (isAuthenticated.value || !modelPlazaRequiresAuth.value))
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => isAdmin.value ? '/admin/dashboard' : '/dashboard')
const entryPath = computed(() => isAuthenticated.value ? dashboardPath.value : '/login')
const currentYear = computed(() => new Date().getFullYear())

const starField = Array.from({ length: 28 }, (_, index) => ({
  left: `${(index * 37 + 11) % 97}%`,
  top: `${(index * 61 + 7) % 92}%`,
  width: `${index % 4 === 0 ? 4 : 2}px`,
  height: `${index % 4 === 0 ? 4 : 2}px`,
  animationDelay: `${(index % 7) * 0.36}s`,
  opacity: `${0.35 + (index % 5) * 0.1}`,
}))

function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark') {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
}

onMounted(() => {
  initTheme()
  authStore.checkAuth()
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }
})
</script>
