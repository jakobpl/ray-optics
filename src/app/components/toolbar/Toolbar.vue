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
  <!-- Desktop Floating Orb Palette -->
  <div 
    id="toolbar" 
    class="orb-palette-container d-none d-lg-block"
    :style="toolbarContainerStyle"
  >
    <div class="orb-palette" :style="toolbarStyle">
      <FileBar layout="desktop" />
      <div class="orb-divider"></div>
      <ToolsBar layout="desktop" />
      <div class="orb-divider"></div>
      <ViewBar layout="desktop" />
      <div class="orb-divider"></div>
      <RayDensityBar layout="desktop" />
      <div class="orb-divider"></div>
      <LayoutAidsBar layout="desktop" />
      <div class="orb-divider"></div>
      <SettingsBar layout="desktop" />
    </div>
  </div>

  <!-- Mobile Toolbar -->
  <div id="toolbar-mobile-collapse" class="d-block d-lg-none">
    <div>
      <div id="toolbar-mobile" class="container-fluid p-0 position-relative" :style="toolbarMobileStyle">
        <div class="container-sm p-0">
          <div class="row justify-content-between m-0">
            <FileBar layout="mobile" />
            <ToolsBar layout="mobile" />
            <ViewBar layout="mobile" />
            <SettingsBar layout="mobile" />
          </div>
        </div>
        <div class="dropdown-menu mobile-dropdown-menu" :style="[mobileDropdownStyle, { display: 'block', 'z-index': 1 }]">
          <!--dummy background for collapse transition-->
          <div>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
/**
 * @module Toolbar
 * @description The Vue component for the floating orb palette toolbar (which contains both the desktop and mobile parts). It is mixed-paradigm code that combines Vue and vanilla JavaScript due to historical reasons.
 */
import FileBar from './FileBar.vue';
import ToolsBar from './ToolsBar.vue';
import ViewBar from './ViewBar.vue';
import SettingsBar from './SettingsBar.vue';
import RayDensityBar from './RayDensityBar.vue';
import LayoutAidsBar from './LayoutAidsBar.vue';
import * as $ from 'jquery';
import { app } from '../../services/app.js'
import { computed, ref, onMounted } from 'vue'
import { useThemeStore } from '../../store/theme'

const f = function (e) {
  const list = document.getElementsByClassName('mobile-dropdown-menu');
  let maxScrollHeight = 0;
  for (let ele of list) {
    const inner = ele.children[0];
    if (inner.scrollHeight > maxScrollHeight) {
      maxScrollHeight = inner.scrollHeight;
    }
  }
  for (let ele of list) {
    ele.style.height = maxScrollHeight + 'px';
  }
}

export default {
  name: 'Toolbar',
  components: {
    FileBar,
    ToolsBar,
    ViewBar,
    SettingsBar,
    RayDensityBar,
    LayoutAidsBar
  },
  setup() {
    const themeStore = useThemeStore()
    
    // Computed styles that adapt to theme - glassmorphism
    const toolbarStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.75)' : 'rgba(20, 20, 25, 0.75)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)',
        border: '1px solid rgba(255, 255, 255, 0.1)',
        boxShadow: 'inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 8px 32px rgba(0, 0, 0, 0.4)'
      }
    })

    // Container style - centered by default
    const toolbarContainerStyle = computed(() => {
      return {
        top: '20px',
        left: '50%',
        transform: 'translateX(-50%)'
      }
    })

    const toolbarMobileStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.9)' : 'rgba(20, 20, 25, 0.9)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)',
        borderBottom: '1px solid rgba(255, 255, 255, 0.1)'
      }
    })

    const mobileDropdownStyle = computed(() => {
      const isLight = themeStore.backgroundIsLight.value
      return {
        backgroundColor: isLight ? 'rgba(30, 30, 35, 0.95)' : 'rgba(20, 20, 25, 0.95)',
        backdropFilter: 'blur(20px) saturate(180%)',
        WebkitBackdropFilter: 'blur(20px) saturate(180%)'
      }
    })

    return {
      toolbarStyle,
      toolbarContainerStyle,
      toolbarMobileStyle,
      mobileDropdownStyle
    }
  },
  mounted() {
    document.getElementById('toolbar-mobile').addEventListener('shown.bs.dropdown', f);
    document.getElementById('toolbar-mobile').addEventListener('hidden.bs.dropdown', f);

    document.getElementById('more-options-dropdown').addEventListener('click', function (e) {
      e.stopPropagation();
    });

    document.getElementById('mobile-dropdown-options').addEventListener('click', function (e) {
      e.stopPropagation();
    });

    // Listen for changes to any radio input within a dropdown
    document.querySelectorAll('.dropdown-menu input[type="radio"]').forEach(function (input) {
      input.addEventListener('change', function () {
        if (input.checked) {
          // Reset other dropdown buttons
          if (!input.id.includes('mobile')) {
            app.resetDropdownButtons();

            // Get the associated dropdown button using the aria-labelledby attribute
            let dropdownButton = document.getElementById(input.closest('.dropdown-menu').getAttribute('aria-labelledby'));

            // Style the button to indicate selection.
            dropdownButton.classList.add('selected');
          } else if (input.name == 'toolsradio_mobile') {
            app.resetDropdownButtons();

            // Get the associated mobile trigger button by searching up the DOM tree
            let element = input;
            let groupId = null;
            while (element && element.parentElement) {
              element = element.parentElement;
              if (element.id && element.id.startsWith('mobile-dropdown-')) {
                groupId = element.id.replace('mobile-dropdown-', '');
                break;
              }
            }
            
            if (groupId) {
              let toggle = document.getElementById(`mobile-dropdown-trigger-${groupId}`);
              if (toggle != null) {
                // Style the button to indicate selection.
                toggle.classList.add('selected');
              }
            }
          }
        }
      });
    });

    // Listen for changes to standalone radio inputs (outside dropdowns)
    document.querySelectorAll('input[type="radio"].btn-check').forEach(function (input) {
      if (input.name == 'toolsradio' && !input.closest('.dropdown-menu') && !input.id.includes('mobile')) { // Check if the radio is not inside a dropdown
        input.addEventListener('change', function () {
          if (input.checked) {
            // Reset dropdown buttons
            app.resetDropdownButtons();
          }
        });
      }
    });

    var currentMobileToolGroupId = null;

    const allElements = document.querySelectorAll('*');

    allElements.forEach(element => {
      if (element.id && element.id.startsWith('mobile-dropdown-trigger-')) {
        const toolGroupId = element.id.replace('mobile-dropdown-trigger-', '');
        const toolGroup = document.getElementById(`mobile-dropdown-${toolGroupId}`);

      element.addEventListener('click', (event) => {
        // Show the corresponding tool group in the mobile tool dropdown.
        event.stopPropagation();
        const originalWidth = $("#mobile-dropdown-tools-root").width();
        const originalMarginLeft = parseInt($("#mobile-dropdown-tools-root").css("margin-left"), 10);
        const originalMarginRight = parseInt($("#mobile-dropdown-tools-root").css("margin-right"), 10);
        $("#mobile-dropdown-tools-root").animate({ "margin-left": -originalWidth, "margin-right": originalWidth }, 300, function () {
          $(this).hide();
          toolGroup.style.display = '';
          $(this).css({
            "margin-left": originalMarginLeft + "px",
            "margin-right": originalMarginRight + "px"
          });
          f();
        });

        currentMobileToolGroupId = toolGroupId;
      });
    }
  });

  document.getElementById('mobile-tools').addEventListener('click', (event) => {
    // Hide the mobile tool dropdown.
    if (currentMobileToolGroupId != null) {
      document.getElementById(`mobile-dropdown-${currentMobileToolGroupId}`).style.display = 'none';
      document.getElementById('mobile-dropdown-tools-root').style.display = '';
      f();
    }
  });
  },
}
</script>

<style scoped>
/* ============================================
   Floating Orb Palette Container
   ============================================ */

.orb-palette-container {
  position: fixed;
  z-index: 1000;
  padding: 0;
  /* Centered positioning handled by inline styles */
}

/* ============================================
   Orb Palette - Glassmorphism Pill
   ============================================ */

.orb-palette {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 8px 16px;
  border-radius: 50px;
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  animation: floatIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
  max-width: 95vw;
  max-height: 80vh;
  overflow-y: auto;
}

@keyframes floatIn {
  0% {
    opacity: 0;
    transform: translateY(-30px) scale(0.9);
  }
  60% {
    transform: translateY(8px) scale(1.02);
  }
  100% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

/* Divider between sections */
.orb-divider {
  width: 1px;
  height: 32px;
  background: linear-gradient(
    to bottom,
    transparent,
    rgba(255, 255, 255, 0.2) 20%,
    rgba(255, 255, 255, 0.2) 80%,
    transparent
  );
  margin: 0 4px;
  transition: all 0.3s ease;
}

/* ============================================
   Mobile Toolbar Styles
   ============================================ */

#toolbar, #toolbar-mobile {
  padding-top: 7px;
  padding-bottom: 3px;
  padding-left: 7px;
  padding-right: 7px;
}

#toolbar-mobile {
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
}

#non-toolbar-space {
  position: relative;
  flex-grow: 1;
}

.mobile-dropdown-menu {
  height: 0;
  transition: height .3s ease;
  overflow: hidden;

  top: 100%;
  left: 0;
  right: 0;
  margin: 0 !important;
  padding: 0;  
  overflow: hidden; 
  
  border: none;
  border-radius: 0;
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
}

#mobile-dropdown-collpase {
  position: absolute;
  top: 0;
  width: 100%;

  height: 0;
  transition: height .3s ease;
}

.range-minus-btn {
  padding-left: 0px;
  padding-right: 7px;
}

.range-plus-btn {
  padding-left: 7px;
  padding-right: 0px;
}

.settings-number {
  background-color: transparent;
  border: none;
  border-bottom: 1px solid rgba(255, 255, 255, 0.3);
  width: 40px;
  text-align: center;
  color: rgba(255, 255, 255, 0.9);
}

.settings-label {
  padding-right: 0px;
  color: rgba(255, 255, 255, 0.7);
}

#more-options-dropdown {
  min-width: 300px;
  width: max-content;
  max-height: 80vh;
  overflow-y: auto;
  background: rgba(30, 30, 35, 0.95);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4);
}

#mobile-dropdown-options .container {
  max-height: 75vh;
  overflow-y: auto;
}

#mobile-dropdown-options .row {
  padding-top: 0;
  padding-bottom: 0;
}

#mobile-dropdown-options .form-check-input {
  height: 1.2em;
  width: 2.4em;
}

.settings-control-row {
  min-height: 30px;
}

/* ============================================
   Responsive Adjustments
   ============================================ */

@media (max-width: 1400px) {
  .orb-palette {
    padding: 6px 12px;
    gap: 3px;
    border-radius: 24px;
  }
  
  .orb-divider {
    height: 28px;
    margin: 0 3px;
  }
}

@media (max-width: 1200px) {
  .orb-palette {
    padding: 6px 10px;
    gap: 2px;
    border-radius: 20px;
  }
  
  .orb-divider {
    height: 24px;
    margin: 0 2px;
  }
}

@media (max-width: 992px) {
  .orb-palette-container {
    display: none !important;
  }
}
</style>
