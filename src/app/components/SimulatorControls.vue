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
  <div id="simulator_controls" class="floating-controls" v-show="showSimulatorControls" :style="[controlStyle, controlContainerStyle]">
    <button class="control-orb" id="refresh_scene" :class="{ 'active': false }" v-tooltip-popover="{ title: $t('simulator:simulatorControls.refreshScene.title') }" @click="handleRefreshScene">
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 18 16">
        <path fill-rule="evenodd" d="M6 3.5a.5.5 0 0 1 .5-.5h8a.5.5 0 0 1 .5.5v9a.5.5 0 0 1-.5.5h-8a.5.5 0 0 1-.5-.5v-2a.5.5 0 0 0-1 0v2A1.5 1.5 0 0 0 6.5 14h8a1.5 1.5 0 0 0 1.5-1.5v-9A1.5 1.5 0 0 0 14.5 2h-8A1.5 1.5 0 0 0 5 3.5v2a.5.5 0 0 0 1 0z"/>
        <path fill-rule="evenodd" d="M11.854 8.354a.5.5 0 0 0 0-.708l-3-3a.5.5 0 1 0-.708.708L10.293 7.5H1.5a.5.5 0 0 0 0 1h8.793l-2.147 2.146a.5.5 0 0 0 .708.708z"/>
      </svg>
    </button>
    <button class="control-orb" id="refresh_simulation" :class="{ 'active': isSimulatorRunning }" v-tooltip-popover="{ title: $t('simulator:simulatorControls.refreshSimulation.title') }" @click="handleRefreshSimulation">
      <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" viewBox="0 0 16 16">
        <path d="m11.596 8.697-6.363 3.692c-.54.313-1.233-.066-1.233-.697V4.308c0-.63.692-1.01 1.233-.696l6.363 3.692a.802.802 0 0 1 0 1.393"/>
      </svg>
    </button>
    <button class="control-orb" :class="{ 'active': isAutoRefreshEnabled, 'pulse': isAutoRefreshEnabled }" id="auto_refresh" v-tooltip-popover="{ title: $t('simulator:simulatorControls.autoRefresh.title') }" @click="handleAutoRefresh">
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16">
        <path d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41m-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9"/>
        <path fill-rule="evenodd" d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5 5 0 0 0 8 3M3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9z"/>
      </svg>
    </button>
  </div>
</template>

<script>
/**
 * @module SimulatorControls
 * @description The Vue component for the simulator controls (refresh, auto-refresh toggle, etc.) at the bottom center of the screen.
 */
import { vTooltipPopover } from '../directives/tooltip-popover'
import { useStatusStore } from '../store/status'
import { usePreferencesStore } from '../store/preferences'
import { useThemeStore } from '../store/theme'
import { computed, ref, toRef } from 'vue'
import { app } from '../services/app'
import { jsonEditorService } from '../services/jsonEditor'

export default {
  name: 'SimulatorControls',
  directives: {
    'tooltip-popover': vTooltipPopover
  },
  setup() {
    const statusStore = useStatusStore()
    const preferences = usePreferencesStore()
    const themeStore = useThemeStore()
    const isSimulatorRunning = computed(() => statusStore.isSimulatorRunning)
    const isPlayButtonPaused = ref(false)
    const isAutoRefreshEnabled = ref(true) // Default to true
    const showSimulatorControls = toRef(preferences, 'showSimulatorControls')
    const showJsonEditor = toRef(preferences, 'showJsonEditor')
    const sidebarWidth = toRef(preferences, 'sidebarWidth')
    
    // Adjust position when sidebar is shown
    const controlStyle = computed(() => {
      const halfSidebarWidth = showJsonEditor.value ? sidebarWidth.value / 2 : 0 // Half of the sidebar width since we're translating from center
      return {
        transform: `translateX(calc(-50% + ${halfSidebarWidth}px))`
      }
    })
    
    // Container style with glassmorphism
    const controlContainerStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.75)' : 'rgba(20, 20, 25, 0.75)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)'
      }
    })
    
    const handleRefreshScene = (event) => {
      event.target.blur()
      
      if (jsonEditorService.aceEditor && !jsonEditorService.isSynced) {
        // If the user is editing the JSON and has not synced it yet, just sync it.
        jsonEditorService.parse()
      } else if (app.editor) {
        // Otherwise, just reload the scene (useful if the scene contains randomization)
        app.editor.loadJSON(app.editor.lastActionJson)
      }
    }

    const handleRefreshSimulation = (event) => {
      event.target.blur()
      if (jsonEditorService.aceEditor && !jsonEditorService.isSynced) {
        jsonEditorService.parse()
      }
      app.simulator.manualRedrawLightLayer()
    }
    
    const handleAutoRefresh = (event) => {
      event.target.blur()
      
      isAutoRefreshEnabled.value = !isAutoRefreshEnabled.value
      
      app.simulator.manualLightRedraw = !isAutoRefreshEnabled.value
      jsonEditorService.manualParse = !isAutoRefreshEnabled.value
      
      if (isAutoRefreshEnabled.value) {
        if (jsonEditorService.aceEditor && !jsonEditorService.isSynced) {
          jsonEditorService.parse()
        }
        app.simulator.manualRedrawLightLayer()
      }
    }

    return {
      isSimulatorRunning,
      isAutoRefreshEnabled,
      showSimulatorControls,
      controlStyle,
      controlContainerStyle,
      handleRefreshScene,
      handleRefreshSimulation,
      handleAutoRefresh
    }
  }
}
</script>

<style scoped>
.floating-controls {
  position: fixed;
  bottom: 30px;
  left: 50%;
  z-index: 100;
  padding: 10px 14px;
  border-radius: 50px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4);
  display: flex;
  gap: 10px;
  animation: controlsFloatIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes controlsFloatIn {
  0% {
    opacity: 0;
    transform: translateX(-50%) translateY(30px) scale(0.9);
  }
  60% {
    transform: translateX(-50%) translateY(-8px) scale(1.02);
  }
  100% {
    opacity: 1;
    transform: translateX(-50%) translateY(0) scale(1);
  }
}

.control-orb {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: rgba(255, 255, 255, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 4px 16px rgba(0, 0, 0, 0.2);
  padding: 0;
}

.control-orb:hover {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  transform: scale(1.15);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 24px rgba(255, 255, 255, 0.2);
}

.control-orb:active {
  transform: scale(0.95);
}

.control-orb.active {
  background: rgba(10, 88, 202, 0.6);
  border-color: rgba(100, 200, 255, 0.3);
  color: white;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 20px rgba(100, 200, 255, 0.4);
}

.control-orb.active:hover {
  background: rgba(10, 88, 202, 0.75);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 0 30px rgba(100, 200, 255, 0.5);
}

.control-orb.pulse {
  animation: orbPulse 2s ease-in-out infinite;
}

@keyframes orbPulse {
  0%, 100% {
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 20px rgba(100, 200, 255, 0.3);
  }
  50% {
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 30px rgba(100, 200, 255, 0.5);
  }
}

.control-orb svg {
  transition: transform 0.3s ease;
}

.control-orb:hover svg {
  transform: scale(1.1);
}
</style>
