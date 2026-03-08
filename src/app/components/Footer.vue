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
  <!-- Footer buttons -->
  <div class="floating-footer-right" id="footer-right">
    <a class="footer-orb" href="https://phydemo.app/ray-optics/" target="_blank" id="home" v-tooltip-popover="{ title: $t('main:pages.home') }">
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16">
        <path d="M8.354 1.146a.5.5 0 0 0-.708 0l-6 6A.5.5 0 0 0 1.5 7.5v7a.5.5 0 0 0 .5.5h4.5a.5.5 0 0 0 .5-.5v-4h2v4a.5.5 0 0 0 .5.5H14a.5.5 0 0 0 .5-.5v-7a.5.5 0 0 0-.146-.354L13 5.793V2.5a.5.5 0 0 0-.5-.5h-1a.5.5 0 0 0-.5.5v1.293L8.354 1.146ZM2.5 14V7.707l5.5-5.5 5.5 5.5V14H10v-4a.5.5 0 0 0-.5-.5h-3a.5.5 0 0 0-.5.5v4H2.5Z"/>
      </svg>
    </a>
    <button class="footer-orb" type="button" id="helpDropdown" data-bs-toggle="dropdown" aria-expanded="false">
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16">
        <path d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"/>
        <path d="M5.255 5.786a.237.237 0 0 0 .241.247h.825c.138 0 .248-.113.266-.25.09-.656.54-1.134 1.342-1.134.686 0 1.314.343 1.314 1.168 0 .635-.374.927-.965 1.371-.673.489-1.206 1.06-1.168 1.987l.003.217a.25.25 0 0 0 .25.246h.811a.25.25 0 0 0 .25-.25v-.105c0-.718.273-.927 1.01-1.486.609-.463 1.244-.977 1.244-2.056 0-1.511-1.276-2.241-2.673-2.241-1.267 0-2.655.59-2.75 2.286zm1.557 5.763c0 .533.425.927 1.01.927.609 0 1.028-.394 1.028-.927 0-.552-.42-.94-1.029-.94-.584 0-1.009.388-1.009.94z"/>
      </svg>
    </button>
    <div class="dropdown-menu glass-dropdown" id="help-dropdown" ref="helpDropdown" aria-labelledby="helpDropdown">
      <div class="container help-dropdown-inner">
        <div class="help-popup-body-wrapper">
          <div class="help-popup-body" id="help_popover_text" ref="helpBody" @scroll="onHelpScroll">
            <div>
              <b v-html="$t('simulator:footer.helpPopup.constrainedDragging.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.constrainedDragging.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.groupRotateScale.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.groupRotateScale.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.editCoordinates.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.editCoordinates.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.arrayFormula.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.arrayFormula.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.keyboardShortcuts.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.keyboardShortcuts.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.runLocally.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.runLocally.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.integrations.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.integrations.description')"></p>
              <b v-html="$t('simulator:footer.helpPopup.contactUs.title')"></b>
              <p v-html="$t('simulator:footer.helpPopup.contactUs.description')"></p>
              <p v-html="$t('simulator:footer.helpPopup.contactUs.contribute')"></p>
            </div>
          </div>
          <div class="scroll-fade-overlay" v-show="showScrollFade"></div>
        </div>
        <div class="help-popup-footer">
          <hr class="dropdown-divider">
          <a href="https://phydemo.app/ray-optics/about" target="_blank" id="about" v-html="$t('main:pages.about')"></a>
          <span class="help-divider-dot" aria-hidden="true">&middot;</span>
          <a href="https://github.com/ricktu288/ray-optics/blob/master/CITATION.md" target="_blank" id="cite-this-project" v-html="$t('main:aboutPage.cite')"></a>
        </div>
      </div>
    </div>

    <a class="footer-orb" id="github" href="https://github.com/ricktu288/ray-optics" target="_blank" v-tooltip-popover="{ title: $t('main:pages.github') }">
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16">
        <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82-1.27.82-2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
      </svg>
    </a>
  </div>
</template>

<script>
/**
 * @module Footer
 * @description The Vue component for the footer buttons on the lower right corner.
 */
import { vTooltipPopover } from '../directives/tooltip-popover'

export default {
  directives: {
    'tooltip-popover': vTooltipPopover
  },
  data() {
    return {
      showScrollFade: true
    }
  },
  mounted() {
    // Re-check scroll state each time the dropdown is shown
    const dropdown = this.$refs.helpDropdown
    if (dropdown) {
      dropdown.addEventListener('shown.bs.dropdown', this.checkScroll)
    }
  },
  methods: {
    onHelpScroll() {
      this.checkScroll()
    },
    checkScroll() {
      const el = this.$refs.helpBody
      if (!el) return
      // Hide the fade when scrolled to (or near) the bottom
      const threshold = 8
      this.showScrollFade = el.scrollHeight - el.scrollTop - el.clientHeight > threshold
    }
  }
}
</script>

<style scoped>
.floating-footer-right {
  position: fixed;
  right: 20px;
  bottom: 20px;
  z-index: 100;
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 12px;
  background: rgba(30, 30, 35, 0.75);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4);
  border-radius: 50px;
  animation: footerFloatIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes footerFloatIn {
  0% {
    opacity: 0;
    transform: translateX(30px) scale(0.9);
  }
  60% {
    transform: translateX(-8px) scale(1.02);
  }
  100% {
    opacity: 1;
    transform: translateX(0) scale(1);
  }
}

.footer-orb {
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
  text-decoration: none;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 4px 16px rgba(0, 0, 0, 0.2);
  padding: 0;
}

.footer-orb:hover {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  transform: scale(1.15);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 24px rgba(255, 255, 255, 0.2);
}

.footer-orb:active {
  transform: scale(0.95);
}

.footer-orb:focus {
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 24px rgba(100, 200, 255, 0.3);
}

/* Help dropdown glass styling */
.glass-dropdown {
  background: rgba(30, 30, 35, 0.95) !important;
  backdrop-filter: blur(20px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(20px) saturate(180%) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 24px 64px rgba(0, 0, 0, 0.5) !important;
  border-radius: 16px !important;
  padding: 12px !important;
}

.help-divider-dot {
  color: rgba(255, 255, 255, 0.3);
  margin: 0 0.35rem;
  font-size: 0.95em;
}

.help-popup-body-wrapper {
  position: relative;
  flex: 1;
  min-height: 0;
  display: flex;
  flex-direction: column;
}

.scroll-fade-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 40px;
  background: linear-gradient(
    to bottom,
    transparent,
    rgba(30, 30, 35, 0.95)
  );
  pointer-events: none;
  z-index: 1;
  transition: opacity 0.2s ease;
}

.help-popup-body {
  color: rgba(255, 255, 255, 0.85);
  font-size: 13px;
  line-height: 1.6;
}

.help-popup-body b {
  color: rgba(255, 255, 255, 0.95);
  font-weight: 600;
  display: block;
  margin-top: 12px;
  margin-bottom: 4px;
}

.help-popup-body b:first-child {
  margin-top: 0;
}

.help-popup-body p {
  margin-bottom: 8px;
}

.help-popup-footer {
  padding-top: 8px;
  font-size: 12px;
}

.help-popup-footer a {
  color: #a9ccff;
  text-decoration: none;
  transition: color 0.2s ease;
}

.help-popup-footer a:hover {
  color: #bdd6f9;
  text-decoration: underline;
}

.dropdown-divider {
  border-color: rgba(255, 255, 255, 0.1) !important;
  margin: 8px 0 !important;
}
</style>
