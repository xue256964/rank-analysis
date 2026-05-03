<template>
  <n-config-provider
    :theme="settingsStore.theme"
    :theme-overrides="themeOverrides"
    :class="{ 'theme-light': !isDark }"
  >
    <n-message-provider>
      <n-notification-provider>
        <n-dialog-provider>
          <n-loading-bar-provider>
            <Framework></Framework>
          </n-loading-bar-provider>
        </n-dialog-provider>
      </n-notification-provider>
    </n-message-provider>

    <!-- ========== ChampR 一键出装（悬浮栏，不影响原界面） ========== -->
    <div
      v-if="showBuildBar"
      style="
        position: fixed;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
        z-index: 9999;
        background: rgba(10, 12, 16, 0.85);
        backdrop-filter: blur(12px);
        border: 1px solid rgba(200, 170, 110, 0.35);
        border-radius: 32px;
        padding: 8px 18px;
        display: flex;
        align-items: center;
        gap: 10px;
        color: #cdbe91;
        font-size: 13px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.5);
      "
    >
      <span style="white-space: nowrap; font-weight: 600;">⚜️ 一键应用出装</span>
      <input
        v-model="champion"
        placeholder="英雄英文名"
        style="
          padding: 6px 10px;
          border-radius: 20px;
          border: 1px solid rgba(200, 170, 110, 0.4);
          background: rgba(0,0,0,0.4);
          color: #f0e6d2;
          width: 130px;
          outline: none;
        "
      />
      <button
        @click="applyBuild"
        style="
          padding: 6px 16px;
          border-radius: 20px;
          border: none;
          background: linear-gradient(180deg, #c8aa6e, #a6894e);
          color: #0a0c10;
          font-weight: bold;
          cursor: pointer;
          white-space: nowrap;
        "
      >
        应用
      </button>
      <span
        style="cursor: pointer; color: #5a5a5a; font-size: 16px;"
        @click="showBuildBar = false"
      >
        ✕
      </span>
    </div>
    <!-- ==================================================== -->
  </n-config-provider>
</template>

<script lang="ts" setup>
import Framework from '@renderer/components/Framework.vue'
import { useSettingsStore } from '@renderer/pinia/setting'
import { computed, ref } from 'vue'
import { GlobalThemeOverrides } from 'naive-ui'
// ChampR 集成
import { invoke } from '@tauri-apps/api/core'

const settingsStore = useSettingsStore()
const champion = ref('')
const result = ref('')
const showBuildBar = ref(true) // 控制悬浮栏显示

async function applyBuild() {
  try {
    result.value = await invoke('apply_champion_build', { champion: champion.value })
  } catch (e: any) {
    result.value = '失败: ' + e
  }
}

const isDark = computed(() => {
  const name = settingsStore.theme?.name
  return name === 'Dark' || name === 'dark'
})

const themeOverrides = computed<GlobalThemeOverrides>(() => {
  if (isDark.value) {
    return {
      common: {
        borderRadius: '8px',
        borderRadiusSmall: '6px'
      },
      Card: {
        borderRadius: '10px',
        color: 'rgba(255,255,255,0.05)',
        boxShadow: '0 2px 8px rgba(0,0,0,0.45), inset 0 1px 0 rgba(255,255,255,0.06)',
        borderColor: 'rgba(255,255,255,0.09)'
      },
      Input: {
        borderRadius: '8px',
        color: 'rgba(255,255,255,0.05)',
        border: '1px solid rgba(255,255,255,0.09)'
      },
      Button: {
        borderRadiusSmall: '6px',
        borderRadiusMedium: '8px'
      },
      Select: {
        borderRadius: '8px'
      },
      Layout: {
        color: '#0a0a0d'
      },
      Menu: {
        itemColorActive: 'rgba(61,155,122,0.14)',
        itemColorActiveHover: 'rgba(61,155,122,0.18)',
        itemBorderRadius: '10px',
        itemTextColorActive: '#3d9b7a',
        itemIconColorActive: '#3d9b7a'
      }
    }
  }
  return {
    common: {
      borderRadius: '8px',
      borderRadiusSmall: '6px'
    },
    Card: {
      borderRadius: '10px',
      color: 'rgba(0,0,0,0.035)',
      boxShadow: '0 2px 8px rgba(0,0,0,0.12), inset 0 1px 0 rgba(255,255,255,0.7)',
      borderColor: 'rgba(0,0,0,0.08)'
    },
    Input: {
      borderRadius: '8px',
      border: '1px solid rgba(0,0,0,0.08)'
    },
    Button: {
      borderRadiusSmall: '6px',
      borderRadiusMedium: '8px'
    },
    Select: {
      borderRadius: '8px'
    },
    Layout: {
      color: '#f0f2f5'
    },
    Menu: {
      itemColorActive: 'rgba(45,138,108,0.12)',
      itemColorActiveHover: 'rgba(45,138,108,0.18)',
      itemBorderRadius: '10px',
      itemTextColorActive: '#2d8a6c',
      itemIconColorActive: '#2d8a6c'
    }
  }
})
</script>
<style lang="css">
html,
body {
  margin: 0;
  /* 禁止 html,body 滚动，避免滚动条出现在标题栏右边 */
  overflow: hidden;
}

.root {
  display: flex;
  flex-direction: column;
  height: 100%;
  background-color: var(--bg-base);
  color: var(--text-primary);
}

.custom-titlebar {
  display: flex;
  align-items: center;
  flex-shrink: 0;
  height: 35px;
  width: 100%;
  z-index: 9999;
  background-color: var(--bg-surface);
  color: var(--text-primary);
  padding-left: 12px;
  font-size: 14px;
}

.content {
  /* 内容区需要设置可滚动 */
  overflow: auto;
}
</style>
