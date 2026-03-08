<!--
  Copyright 2025 The Ray Optics Simulation authors and contributors

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->

<template>
  <div class="floating-status-area" id="footer-left" :style="notificationStyle">
    <div class="status-inline">
      <span
        v-show="betaFeatures.length > 0"
        class="beta-badge"
        v-tooltip-popover:popover="{
          title: $t('simulator:footer.betaFeatures.title'),
          content: betaPopoverContent,
          trigger: 'click',
          placement: 'top',
          html: true
        }"
        @click.stop
      >
        Beta
      </span>
      <div id="forceStop" v-show="simulatorStatus?.isSimulatorRunning" @click="handleForceStop" :style="forceStopStyle">
        <div class="spinner-ring" role="status"></div>
        <span v-html="$t('simulator:footer.processing')"></span>
      </div>
    </div>
    
    <div id="status" class="status-card" v-show="showStatus" :style="statusStyle">
      <div class="status-content" v-html="formattedMousePosition"></div>
      <div class="status-content" v-html="formattedSimulatorStatus.join('<br>')"></div>
    </div>
    
    <div id="warning" class="alert-card alert-warning" v-show="warnings.length > 0">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 20">
        <path d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5m.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2"/>
      </svg>
      <span v-html="warnings.join('<br>')"></span>
    </div>
    
    <div id="error" class="alert-card alert-error" v-show="errors.length > 0">
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 20">
        <path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0M8 4a.905.905 0 0 0-.9.995l.35 3.507a.552.552 0 0 0 1.1 0l.35-3.507A.905.905 0 0 0 8 4m.002 6a1 1 0 1 0 0 2 1 1 0 0 0 0-2"/>
      </svg>
      <span v-html="errors.join('<br>')"></span>
    </div>
    
    <VirtualKeyboard />
  </div>
</template>

<script>
/**
 * @module StatusArea
 * @description The Vue component for the status area (including mouse coordinates, simulator status, warnings and errors) at the lower left corner.
 */
import { usePreferencesStore } from '../store/preferences'
import { useThemeStore } from '../store/theme'
import { useStatus } from '../composables/useStatus'
import { computed, toRef } from 'vue'
import i18next from 'i18next'
import { app } from '../services/app'
import VirtualKeyboard from './VirtualKeyboard.vue'
import { vTooltipPopover } from '../directives/tooltip-popover'
import { parseLinks } from '../utils/links.js'

export default {
  name: 'StatusArea',
  components: {
    VirtualKeyboard
  },
  directives: {
    'tooltip-popover': vTooltipPopover
  },
  setup() {
    const preferences = usePreferencesStore()
    const themeStore = useThemeStore()
    const status = useStatus()
    const showJsonEditor = toRef(preferences, 'showJsonEditor')
    const sidebarWidth = toRef(preferences, 'sidebarWidth')
    
    const notificationStyle = computed(() => ({
      left: showJsonEditor.value ? `${sidebarWidth.value + 20}px` : '20px'
    }))

    // Computed styles that adapt to theme - status overlay uses glassmorphism
    const statusStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      const textColor = isLight ? 'rgba(255, 255, 255, 0.9)' : 'rgba(255, 255, 255, 0.85)'
      
      return {
        color: textColor,
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.7)' : 'rgba(20, 20, 25, 0.7)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)'
      }
    })

    const forceStopStyle = computed(() => {
      return {
        color: 'rgba(255, 255, 255, 0.8)'
      }
    })

    const handleForceStop = () => {
      app.simulator.stopSimulation()
    }

    const betaPopoverContent = computed(() => {
      if (!status.activeBetaFeatures.value?.length) {
        return ''
      }

      const listItems = status.activeBetaFeatures.value
        .map((feature) => `<li>${feature}</li>`)
        .join('')

      const description = i18next.t('simulator:footer.betaFeatures.description')
      const details = i18next.t('simulator:footer.betaFeatures.details')
      return parseLinks(`${description}<ul>${listItems}</ul>${details}`)
    })

    return {
      showStatus: preferences.showStatus,
      notificationStyle,
      statusStyle,
      forceStopStyle,
      // From status composable
      formattedMousePosition: status.formattedMousePosition,
      formattedSimulatorStatus: status.formattedSimulatorStatus,
      errors: status.activeErrors,
      warnings: status.activeWarnings,
      betaFeatures: status.activeBetaFeatures,
      simulatorStatus: status.simulatorStatus,
      betaPopoverContent,
      // Methods
      handleForceStop
    }
  }
}
</script>

<style scoped>
.floating-status-area {
  position: fixed;
  bottom: 20px;
  z-index: 100;
  padding-right: 80px;
  pointer-events: none;
  display: flex;
  flex-direction: column;
  gap: 8px;
  animation: statusFloatIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes statusFloatIn {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  70% {
    transform: translateY(-5px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

#forceStop {
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  pointer-events: auto;
  padding: 6px 12px;
  background: rgba(30, 30, 35, 0.7);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  font-size: 12px;
  transition: all 0.25s ease;
}

#forceStop:hover {
  background: rgba(50, 50, 55, 0.8);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.3);
}

.spinner-ring {
  width: 16px;
  height: 16px;
  border: 2px solid rgba(255, 255, 255, 0.2);
  border-top-color: rgba(255, 255, 255, 0.9);
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.status-inline {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  pointer-events: auto;
}

.beta-badge {
  color: rgba(242, 141, 40, 0.9);
  border: 1px solid currentColor;
  border-radius: 12px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: 22px;
  padding: 0 10px;
  box-sizing: border-box;
  font-size: 0.7em;
  font-weight: 600;
  line-height: 1;
  cursor: pointer;
  background: rgba(242, 141, 40, 0.1);
  backdrop-filter: blur(10px);
  transition: all 0.25s ease;
  pointer-events: auto;
}

.beta-badge:hover {
  background: rgba(242, 141, 40, 0.2);
  box-shadow: 0 0 16px rgba(242, 141, 40, 0.3);
}

/* Status Card */
.status-card {
  border-radius: 12px;
  padding: 10px 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.05), 0 8px 32px rgba(0, 0, 0, 0.3);
  font-family: 'SF Mono', Monaco, Inconsolata, 'Fira Code', monospace;
  font-size: 12px;
  pointer-events: auto;
  width: fit-content;
  transition: all 0.3s ease;
}

.status-card:hover {
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 12px 40px rgba(0, 0, 0, 0.4);
}

.status-content {
  line-height: 1.5;
}

/* Alert Cards */
.alert-card {
  border-radius: 12px;
  padding: 10px 14px;
  font-family: 'SF Mono', Monaco, Inconsolata, 'Fira Code', monospace;
  font-size: 12px;
  display: flex;
  align-items: flex-start;
  gap: 10px;
  pointer-events: auto;
  max-width: 400px;
  border: 1px solid;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  animation: alertSlideIn 0.4s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes alertSlideIn {
  0% {
    opacity: 0;
    transform: translateX(-20px);
  }
  60% {
    transform: translateX(5px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}

.alert-card svg {
  flex-shrink: 0;
  margin-top: 2px;
}

.alert-warning {
  color: rgba(255, 255, 255, 0.95);
  background: rgba(234, 179, 8, 0.8);
  backdrop-filter: blur(10px);
  border-color: rgba(234, 179, 8, 0.4);
}

.alert-error {
  color: rgba(255, 255, 255, 0.95);
  background: rgba(220, 53, 69, 0.8);
  backdrop-filter: blur(10px);
  border-color: rgba(220, 53, 69, 0.4);
}
</style>
