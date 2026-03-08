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
  <div id="sidebar" v-show="showJsonEditor" :style="[sidebarStyle, { width: sidebarWidth + 'px' }]" :data-width="sidebarWidth">
    <div id="sidebarMobileHeightDiff" class="d-none d-lg-block"></div>
    <div id="jsonEditorContainer">
      <div id="jsonEditor"></div>
      <div class="json-editor-button">
        <a href="https://chatgpt.com/g/g-6777588b53708191b66722e353e95125-ray-optics-coder" target="_blank" class="btn btn-ai-assistant" tabindex="-1" @click="blurButton" v-tooltip-popover:[tooltipType]="{ title: $t('simulator:sidebar.aiAssistant.title'), content: $t('simulator:sidebar.aiAssistant.description'), placement: 'left' }">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-magic" viewBox="0 0 16 16">
            <path d="M9.5 2.672a.5.5 0 1 0 1 0V.843a.5.5 0 0 0-1 0zm4.5.035A.5.5 0 0 0 13.293 2L12 3.293a.5.5 0 1 0 .707.707zM7.293 4A.5.5 0 1 0 8 3.293L6.707 2A.5.5 0 0 0 6 2.707zm-.621 2.5a.5.5 0 1 0 0-1H4.843a.5.5 0 1 0 0 1zm8.485 0a.5.5 0 1 0 0-1h-1.829a.5.5 0 0 0 0 1zM13.293 10A.5.5 0 1 0 14 9.293L12.707 8a.5.5 0 1 0-.707.707zM9.5 11.157a.5.5 0 0 0 1 0V9.328a.5.5 0 0 0-1 0zm1.854-5.097a.5.5 0 0 0 0-.706l-.708-.708a.5.5 0 0 0-.707 0L8.646 5.94a.5.5 0 0 0 0 .707l.708.708a.5.5 0 0 0 .707 0l1.293-1.293Zm-3 3a.5.5 0 0 0 0-.706l-.708-.708a.5.5 0 0 0-.707 0L.646 13.94a.5.5 0 0 0 0 .707l.708.708a.5.5 0 0 0 .707 0z"/>
          </svg>
        </a>
      </div>
    </div>
    <div 
      class="resize-handle"
      @mousedown="startResize"
      @touchstart="startResize"
    ></div>
  </div>
</template>

<script>
/**
 * @module Sidebar
 * @description The Vue component for the sidebar containing the JSON editor.
 */
import { usePreferencesStore } from '../store/preferences'
import { vTooltipPopover } from '../directives/tooltip-popover'
import { computed, toRef, ref, onMounted, onUnmounted } from 'vue'
import { jsonEditorService } from '../services/jsonEditor'
import { useThemeStore } from '../store/theme'

export default {
  name: 'Sidebar',
  directives: {
    'tooltip-popover': vTooltipPopover
  },
  setup() {
    const preferences = usePreferencesStore()
    const themeStore = useThemeStore()
    const help = toRef(preferences, 'help')
    const sidebarWidth = toRef(preferences, 'sidebarWidth')
    const tooltipType = computed(() => help.value ? 'popover' : undefined)
    
    const isResizing = ref(false)
    const startX = ref(0)
    const startWidth = ref(0)
    
    // Computed style with glassmorphism
    const sidebarStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.8)' : 'rgba(20, 20, 25, 0.85)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)'
      }
    })
    
    // Keyboard event handler to prevent propagation
    const handleKeyboardEvent = (e) => {
      // Stop the event from propagating to the body
      e.stopPropagation()
    }
    
    const startResize = (e) => {
      e.preventDefault() // Prevent default touch behavior
      const initialWidth = sidebarWidth.value
      isResizing.value = true
      
      // Handle both mouse and touch events
      const clientX = e.type === 'touchstart' ? e.touches[0].clientX : e.clientX
      startX.value = clientX
      startWidth.value = initialWidth
      
      // Add both mouse and touch event listeners
      document.addEventListener('mousemove', handleResize)
      document.addEventListener('mouseup', stopResize)
      document.addEventListener('touchmove', handleResize, { passive: false })
      document.addEventListener('touchend', stopResize)
      
      document.body.style.cursor = 'ew-resize'
      document.body.style.userSelect = 'none'
    }
    
    const handleResize = (e) => {
      if (!isResizing.value) return
      
      // Prevent default touch behavior
      if (e.type === 'touchmove') {
        e.preventDefault()
      }
      
      // Handle both mouse and touch events
      const clientX = e.type === 'touchmove' ? e.touches[0].clientX : e.clientX
      const deltaX = clientX - startX.value
      const newWidth = Math.max(250, Math.min(800, startWidth.value + deltaX))
      
      sidebarWidth.value = newWidth
      
      // Trigger Ace editor resize with a small delay
      setTimeout(() => {
        if (jsonEditorService.aceEditor) {
          jsonEditorService.aceEditor.resize()
        }
      }, 0)
    }
    
    const stopResize = () => {
      isResizing.value = false
      
      // Remove both mouse and touch event listeners
      document.removeEventListener('mousemove', handleResize)
      document.removeEventListener('mouseup', stopResize)
      document.removeEventListener('touchmove', handleResize)
      document.removeEventListener('touchend', stopResize)
      
      document.body.style.cursor = ''
      document.body.style.userSelect = ''
    }

    const blurButton = () => {
      const button = document.querySelector('.btn-ai-assistant');
      if (button) {
        button.blur();
      }
    };
    
    onMounted(() => {
      // Add keyboard event listeners to prevent propagation from JSON editor
      const jsonEditor = document.getElementById('jsonEditor')
      
      if (jsonEditor) {
        jsonEditor.addEventListener('keydown', handleKeyboardEvent, false)
        jsonEditor.addEventListener('keyup', handleKeyboardEvent, false)
        jsonEditor.addEventListener('keypress', handleKeyboardEvent, false)
      }
    })
    
    onUnmounted(() => {
      // Clean up event listeners if component is destroyed during resize
      document.removeEventListener('mousemove', handleResize)
      document.removeEventListener('mouseup', stopResize)
      document.removeEventListener('touchmove', handleResize)
      document.removeEventListener('touchend', stopResize)
      
      // Clean up keyboard event listeners
      const jsonEditor = document.getElementById('jsonEditor')
      
      if (jsonEditor) {
        jsonEditor.removeEventListener('keydown', handleKeyboardEvent, false)
        jsonEditor.removeEventListener('keyup', handleKeyboardEvent, false)
        jsonEditor.removeEventListener('keypress', handleKeyboardEvent, false)
      }
    })
    
    return {
      showJsonEditor: preferences.showJsonEditor,
      sidebarWidth,
      tooltipType,
      startResize,
      blurButton,
      sidebarStyle
    }
  }
}
</script>

<style scoped>
#sidebar {
  position: absolute;
  z-index: -2;
  top: 80px;
  left: 20px;
  max-width: 100%;
  height: calc(100% - 100px);
  display: flex;
  flex-direction: column;
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4);
  overflow: hidden;
  animation: sidebarSpringIn 0.5s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes sidebarSpringIn {
  0% {
    opacity: 0;
    transform: translateX(-30px) scale(0.95);
  }
  60% {
    transform: translateX(8px) scale(1.01);
  }
  100% {
    opacity: 1;
    transform: translateX(0) scale(1);
  }
}

#sidebarMobileHeightDiff {
  height: 22px;
}

#jsonEditorContainer {
  width: 100%;
  flex-grow: 1;
  background: rgba(25, 25, 30, 0.6);
  position: relative;
  border-radius: 16px;
}

#jsonEditor {
  width: 100%;
  height: 100%
}

.json-editor-button {
  position: absolute;
  right: 15px;
  bottom: 15px;
  z-index: 10;
}

.btn-ai-assistant {
  color: rgba(255, 255, 255, 0.7);
  padding: 5px;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(10px);
  border-radius: 50%;
  border: 1px solid rgba(255, 255, 255, 0.1);
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 4px 16px rgba(0, 0, 0, 0.2);
}

.btn-ai-assistant:hover, .btn-ai-assistant:focus {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 20px rgba(100, 200, 255, 0.3);
  transform: scale(1.1);
}

.resize-handle {
  position: absolute;
  top: 0;
  right: 0;
  width: 6px;
  height: 100%;
  cursor: ew-resize;
  background: transparent;
  transition: background 0.2s ease;
}

.resize-handle:hover {
  background: rgba(255, 255, 255, 0.15);
}

.resize-handle::after {
  content: '';
  position: absolute;
  top: 50%;
  right: 1px;
  transform: translateY(-50%);
  width: 4px;
  height: 40px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 2px;
  opacity: 0;
  transition: opacity 0.2s ease;
}

.resize-handle:hover::after {
  opacity: 1;
}

/* Custom Ace Editor styling for glassmorphism theme */
:deep(.ace_gutter) {
  background: rgba(20, 20, 25, 0.5) !important;
  color: rgba(255, 255, 255, 0.4) !important;
}

:deep(.ace_scroller) {
  background: transparent !important;
}

:deep(.ace_content) {
  background: transparent !important;
}
</style>
