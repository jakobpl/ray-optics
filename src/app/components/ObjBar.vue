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
  <!-- Main ObjBar - Hidden by default, shown via vanilla JS -->
  <div 
    id="obj_bar" 
    class="floating-contextual-hud"
    :class="{ 'hud-visible': isVisible, 'hud-near-selection': isNearSelection }"
    :style="[objBarStyle, hudPositionStyle]"
  >
    <!-- HUD Header with object name -->
    <div class="hud-header">
      <span class="hud-title" id="obj_name"></span>
      <button class="hud-close" @click="handleUnselect" v-tooltip-popover="{ title: $t('simulator:objBar.unselect.title') }">
        <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" fill="currentColor" viewBox="0 0 16 16">
          <path d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708z"/>
        </svg>
      </button>
    </div>
    
    <!-- HUD Body with properties -->
    <div class="hud-body" id="obj_bar_main">
      <!-- Dynamic content inserted here by the app -->
    </div>
    
    <!-- HUD Footer with actions -->
    <div class="hud-footer">
      <span class="d-none d-lg-inline action-group">
        <button 
          class="hud-action-btn" 
          id="showAdvanced" 
          v-text="$t('simulator:objBar.showAdvanced.title')" 
          @click="handleShowAdvanced"
          v-if="hasAdvancedOptions"
        ></button>
        
        <span id="apply_to_all_box" class="hud-checkbox-wrapper">
          <input type="checkbox" class="hud-checkbox" id="apply_to_all" autocomplete="off" v-model="applyToAll">
          <label 
            id="apply_to_all_label" 
            class="hud-action-btn" 
            for="apply_to_all" 
            v-tooltip-popover="{ title: $t('simulator:objBar.applyToAll.title') }"
            :class="{ 'active': applyToAll }"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" fill="currentColor" viewBox="0 0 16 16">
              <path d="m11.5 11.932a1.5 1.5 0 1 0 0 3 1.5 1.5 0 0 0 0-3zm-2.45 1a2.5 2.5 0 0 1 4.9 0h2.05v1h-2.05a2.5 2.5 0 0 1-4.9 0h-9.05v-1z"/>
              <path d="m11.5 1.1864a1.5 1.5 0 1 0 0 3 1.5 1.5 0 0 0 0-3zm-2.45 1a2.5 2.5 0 0 1 4.9 0h2.05v1h-2.05a2.5 2.5 0 0 1-4.9 0h-9.05v-1z"/>
              <path d="m10.049 6.3809v3.1367h1v-3.1367z"/>
              <path d="m11.82 6.3809v3.1367h1v-3.1367z"/>
            </svg>
          </label>
        </span>
      </span>
      
      <div class="hud-actions">
        <button class="hud-icon-btn" id="copy" v-tooltip-popover="{ title: $t('simulator:objBar.duplicate.title') }" @click="handleClone">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 9.766 9.766">
            <g transform="matrix(.01973 0 0 .01973 .0020532 .061476)">
              <path d="m314.25 85.4h-227c-21.3 0-38.6 17.3-38.6 38.6v325.7c0 21.3 17.3 38.6 38.6 38.6h227c21.3 0 38.6-17.3 38.6-38.6v-325.7c-0.1-21.3-17.4-38.6-38.6-38.6zm11.5 364.2c0 6.4-5.2 11.6-11.6 11.6h-227c-6.4 0-11.6-5.2-11.6-11.6v-325.6c0-6.4 5.2-11.6 11.6-11.6h227c6.4 0 11.6 5.2 11.6 11.6z"/>
              <path d="m401.05 0h-227c-21.3 0-38.6 17.3-38.6 38.6 0 7.5 6 13.5 13.5 13.5s13.5-6 13.5-13.5c0-6.4 5.2-11.6 11.6-11.6h227c6.4 0 11.6 5.2 11.6 11.6v325.7c0 6.4-5.2 11.6-11.6 11.6-7.5 0-13.5 6-13.5 13.5s6 13.5 13.5 13.5c21.3 0 38.6-17.3 38.6-38.6v-325.7c0-21.3-17.3-38.6-38.6-38.6z"/>
            </g>
          </svg>
        </button>
        
        <button class="hud-icon-btn danger" id="delete" v-tooltip-popover="{ title: $t('simulator:objBar.delete.title') }" @click="handleDelete">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
            <path d="M6.5 1h3a.5.5 0 0 1 .5.5v1H6v-1a.5.5 0 0 1 .5-.5ZM11 2.5v-1A1.5 1.5 0 0 0 9.5 0h-3A1.5 1.5 0 0 0 5 1.5v1H2.506a.58.58 0 0 0-.01 0H1.5a.5.5 0 0 0 0 1h.538l.853 10.66A2 2 0 0 0 4.885 16h6.23a2 2 0 0 0 1.994-1.84l.853-10.66h.538a.5.5 0 0 0 0-1h-.995a.59.59 0 0 0-.01 0H11Zm1.958 1-.846 10.58a1 1 0 0 1-.997.92h-6.23a1 1 0 0 1-.997-.92L3.042 3.5h9.916Zm-7.487 1a.5.5 0 0 1 .528.47l.5 8.5a.5.5 0 0 1-.998.06L5 5.03a.5.5 0 0 1 .47-.53Zm5.058 0a.5.5 0 0 1 .47.53l-.5 8.5a.5.5 0 1 1-.998-.06l.5-8.5a.5.5 0 0 1 .528-.47ZM8 4.5a.5.5 0 0 1 .5.5v8.5a.5.5 0 0 1-1 0V5a.5.5 0 0 1 .5-.5Z"/>
          </svg>
        </button>
      </div>
    </div>
    
    <!-- Connector line to selected object -->
    <div class="hud-connector" v-if="isNearSelection"></div>
  </div>
  
  <!-- Mobile menu elements (preserved from original) -->
  <span class="d-none d-lg-inline" id="showAdvanced_container" style="display: none;"></span>
  <span class="d-none d-lg-inline" id="apply_to_all_mobile_container" style="display: none;"></span>
</template>

<script>
/**
 * @module ObjBar
 * @description The Vue component for the floating contextual HUD (object bar). It is mixed-paradigm code that combines Vue and vanilla JavaScript due to historical reasons.
 */
import { computed, toRef, ref, onMounted, onUnmounted, watch, nextTick } from 'vue'
import { vTooltipPopover } from '../directives/tooltip-popover'
import { usePreferencesStore } from '../store/preferences'
import { useThemeStore } from '../store/theme'
import { app } from '../services/app'

export default {
  name: 'ObjBar',
  directives: {
    'tooltip-popover': vTooltipPopover
  },
  setup() {
    const preferences = usePreferencesStore()
    const themeStore = useThemeStore()
    const help = toRef(preferences, 'help')
    const tooltipType = computed(() => help.value ? 'popover' : null)
    
    // HUD state - controlled by vanilla JS, Vue just observes
    const isVisible = ref(false)
    const isNearSelection = ref(false)
    const hudPosition = ref({ x: window.innerWidth / 2, y: 280 })
    const hasAdvancedOptions = ref(false)

    const applyToAll = computed({
      get: () => app.objBar?.shouldApplyToAll ?? false,
      set: (value) => { if (app.objBar) app.objBar.shouldApplyToAll = value }
    })

    // Computed style that adapts to theme with glassmorphism
    const objBarStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.85)' : 'rgba(20, 20, 25, 0.85)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)',
        border: '1px solid rgba(255, 255, 255, 0.1)',
        boxShadow: 'inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4)'
      }
    })
    
    // Dynamic HUD positioning
    const hudPositionStyle = computed(() => {
      return {
        left: `${hudPosition.value.x}px`,
        top: `${hudPosition.value.y}px`,
        transform: 'translate(-50%, -100%)'
      }
    })

    // Watch for object selection and update HUD position
    const updateHudPosition = () => {
      if (!app.editor || !app.editor.selectedObj) {
        return
      }
      
      const obj = app.editor.selectedObj
      
      // Calculate position based on selected object
      const scale = app.scene.scale
      const origin = app.scene.origin
      
      // Get actual HUD height for better positioning
      const hudEl = document.getElementById('obj_bar')
      const hudHeight = hudEl ? hudEl.offsetHeight : 250
      const minTopSpace = 180 // Minimum pixels from top of screen (clearance for orb-palette)
      const minBottomSpace = 80 // Minimum pixels from bottom of screen
      const offsetFromObject = 40 // Distance between object and HUD
      
      // Calculate screen Y position of the object
      let objectScreenY = 0
      let targetX = window.innerWidth / 2
      let targetY = 250 // Default Y position - well below the top
      
      if (obj.p1 && obj.p2) {
        // Line-like objects
        const midX = (obj.p1.x + obj.p2.x) / 2
        const midY = Math.min(obj.p1.y, obj.p2.y)
        objectScreenY = origin.y + midY * scale
        targetX = origin.x + midX * scale
        targetY = objectScreenY - offsetFromObject
        isNearSelection.value = true
      } else if (obj.c) {
        // Circle-like objects
        objectScreenY = origin.y + (obj.c.y - (obj.r || 50)) * scale
        targetX = origin.x + obj.c.x * scale
        targetY = objectScreenY - offsetFromObject
        isNearSelection.value = true
      } else if (obj.x !== undefined && obj.y !== undefined) {
        // Point-like objects
        objectScreenY = origin.y + obj.y * scale
        targetX = origin.x + obj.x * scale
        targetY = objectScreenY - offsetFromObject - 20
        isNearSelection.value = true
      } else {
        // Default: center of screen with good clearance from top
        targetX = window.innerWidth / 2
        targetY = Math.min(window.innerHeight * 0.4, 350) // At most 40% down or 350px
        isNearSelection.value = false
      }
      
      // Check if HUD would go above the top of the screen
      // The HUD is positioned with transform: translate(-50%, -100%), so targetY is the bottom edge of HUD
      if (targetY - hudHeight < minTopSpace) {
        // Position HUD at minimum safe distance from top instead of far below object
        targetY = minTopSpace + hudHeight + 20
        isNearSelection.value = false // Don't show connector when repositioned
      }
      
      // Check if HUD would go below the bottom of the screen
      if (targetY > window.innerHeight - minBottomSpace) {
        targetY = window.innerHeight - minBottomSpace
      }
      
      // Ensure minimum distance from top (safety check)
      if (targetY < minTopSpace + hudHeight) {
        targetY = minTopSpace + hudHeight
      }
      
      // Keep horizontal position within screen bounds
      const horizontalPadding = 220
      targetX = Math.max(horizontalPadding, Math.min(window.innerWidth - horizontalPadding, targetX))
      
      hudPosition.value = { x: targetX, y: targetY }
    }

    // Listen for selection changes from vanilla JS
    let originalSelectObj = null
    let originalUnselect = null
    
    onMounted(() => {
      // Wait for app to be ready
      const checkApp = setInterval(() => {
        if (app.editor && app.editor.selectObj) {
          clearInterval(checkApp)
          
          // Wrap selectObj to detect changes
          originalSelectObj = app.editor.selectObj.bind(app.editor)
          app.editor.selectObj = function(...args) {
            const result = originalSelectObj(...args)
            nextTick(() => {
              if (app.editor.selectedObjIndex >= 0) {
                isVisible.value = true
                // Delay position calculation to ensure DOM is ready for accurate height measurement
                requestAnimationFrame(() => {
                  updateHudPosition()
                })
              }
            })
            return result
          }
          
          // Wrap unselect
          if (app.editor.unselect) {
            originalUnselect = app.editor.unselect.bind(app.editor)
            app.editor.unselect = function(...args) {
              isVisible.value = false
              return originalUnselect(...args)
            }
          }
        }
      }, 100)
      
      window.addEventListener('resize', updateHudPosition)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', updateHudPosition)
      // Restore original methods
      if (app.editor && originalSelectObj) {
        app.editor.selectObj = originalSelectObj
      }
      if (app.editor && originalUnselect) {
        app.editor.unselect = originalUnselect
      }
    })

    return {
      tooltipType,
      applyToAll,
      objBarStyle,
      hudPositionStyle,
      isVisible,
      isNearSelection,
      hasAdvancedOptions
    }
  },
  methods: {
    handleClone(event) {
      event.target.blur()
      app.cloneSelectedObj()
    },
    handleDelete(event) {
      event.target.blur()
      app.deleteSelectedObj()
    },
    handleUnselect(event) {
      event.target.blur()
      app.unselect()
    },
    handleShowAdvanced(event) {
      event.target.blur()
      app.showAdvanced()
    }
  }
}
</script>

<style>
/* ============================================
   Floating Contextual HUD - Glassmorphism
   ============================================ */

.floating-contextual-hud {
  position: fixed;
  z-index: 1000;
  min-width: 280px;
  max-width: 400px;
  border-radius: 16px;
  padding: 0;
  color: rgba(255, 255, 255, 0.9);
  font-size: 12pt;
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  flex-direction: column;
  display: none; /* Hidden by default, shown via vanilla JS */
  overflow: visible; /* Allow content to determine size naturally */
}

.floating-contextual-hud.hud-visible {
  display: flex;
  opacity: 1;
  visibility: visible;
  pointer-events: auto;
  animation: hudSpringIn 0.5s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes hudSpringIn {
  0% {
    opacity: 0;
    transform: translate(-50%, -90%) scale(0.8);
  }
  60% {
    transform: translate(-50%, -102%) scale(1.02);
  }
  100% {
    opacity: 1;
    transform: translate(-50%, -100%) scale(1);
  }
}

/* Connector line to selected object */
.floating-contextual-hud.hud-near-selection .hud-connector {
  position: absolute;
  left: 50%;
  top: 100%;
  width: 2px;
  height: 24px;
  background: linear-gradient(
    to bottom,
    rgba(255, 255, 255, 0.3),
    transparent
  );
  transform: translateX(-50%);
}

/* Decorative gradient border */
.floating-contextual-hud::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: 16px;
  padding: 1px;
  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.2) 0%,
    rgba(255, 255, 255, 0.05) 50%,
    rgba(255, 255, 255, 0.1) 100%
  );
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}

/* ============================================
   HUD Header
   ============================================ */

.hud-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.hud-title {
  font-weight: 600;
  font-size: 13px;
  color: rgba(255, 255, 255, 0.9);
  text-transform: capitalize;
}

.hud-close {
  background: none;
  border: none;
  color: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  padding: 4px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

.hud-close:hover {
  color: rgba(255, 255, 255, 0.9);
  background: rgba(255, 255, 255, 0.1);
}

/* ============================================
   HUD Body - Properties Area
   ============================================ */

.hud-body {
  padding: 12px 16px;
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  align-items: center;
  justify-content: center;
}

/* Style for dynamically inserted controls */
.hud-body .obj-bar-nobr {
  white-space: nowrap;
  padding: 4px 8px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.hud-body input[type="text"],
.hud-body input[type="number"] {
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  padding: 4px 8px;
  color: rgba(255, 255, 255, 0.9);
  font-size: 13px;
  width: 60px;
  text-align: center;
}

.hud-body input[type="text"]:focus,
.hud-body input[type="number"]:focus {
  outline: none;
  border-color: rgba(100, 200, 255, 0.4);
  box-shadow: 0 0 0 2px rgba(100, 200, 255, 0.1);
}

.hud-body .form-range {
  width: 100px;
  height: 4px;
}

.hud-body .form-range::-webkit-slider-thumb {
  width: 14px;
  height: 14px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
}

/* ============================================
   HUD Footer - Actions
   ============================================ */

.hud-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 16px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  gap: 12px;
}

.action-group {
  display: flex;
  align-items: center;
  gap: 8px;
}

.hud-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.hud-action-btn {
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  padding: 6px 12px;
  color: rgba(255, 255, 255, 0.8);
  font-size: 12px;
  cursor: pointer;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.05);
}

.hud-action-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  transform: translateY(-1px);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 4px 12px rgba(0, 0, 0, 0.2);
}

.hud-action-btn.active {
  background: rgba(10, 88, 202, 0.6);
  border-color: rgba(100, 200, 255, 0.3);
  color: white;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 16px rgba(100, 200, 255, 0.3);
}

.hud-icon-btn {
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.8);
  cursor: pointer;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.05);
}

.hud-icon-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  transform: scale(1.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 0 16px rgba(255, 255, 255, 0.15);
}

.hud-icon-btn.danger:hover {
  background: rgba(220, 53, 69, 0.6);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 0 16px rgba(220, 53, 69, 0.3);
}

/* Checkbox wrapper */
.hud-checkbox-wrapper {
  position: relative;
}

.hud-checkbox {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.hud-checkbox + label {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  padding: 0;
}

.hud-checkbox:checked + label {
  background: rgba(10, 88, 202, 0.6);
  border-color: rgba(100, 200, 255, 0.3);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 16px rgba(100, 200, 255, 0.3);
}

/* ============================================
   Legacy obj-bar class support
   ============================================ */

#obj-bar {
  display: none;
}

.obj-bar {
  display: none;
}

/* Legacy styles for dynamically inserted content */
.obj-bar-editable {
  color: white;
  background-color: rgba(0,0,0,0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  text-align: center;
  padding: 4px 8px;
}

.obj-bar-editable::selection {
  background-color: rgba(100, 200, 255, 0.4);
  color: white;
}

.obj-bar-number {
  width: 50px;
  padding-left: 4px;
  padding-right: 4px;
  font-size: 13px;
}

.mq-cursor {
  border-color: white !important;
}

.info-icon {
  color: rgba(255, 255, 255, 0.5);
  padding: 4px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.info-icon:hover {
  color: rgba(255, 255, 255, 0.9);
}

.obj-bar-vue-control {
  display: inline-flex;
  vertical-align: middle;
  align-items: center;
}

/* Form switch in HUD */
.hud-body .form-switch {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  margin: 0;
}

.hud-body .form-switch .form-check-input {
  background-color: rgba(255,255,255, 0.15);
  border: none;
  width: 40px;
  height: 20px;
}

.hud-body .form-switch .form-check-input:checked {
  background-color: rgba(10, 88, 202, 0.8);
  box-shadow: 0 0 10px rgba(100, 200, 255, 0.3);
}
</style>
