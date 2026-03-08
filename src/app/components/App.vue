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
  <CanvasContainer />
  <WelcomeMessage />
  <Sidebar />
  <StatusArea />
  <Toolbar />
  <ObjBar />
  <SimulatorControls />
  <Footer />
  <ModuleModal />
  <SaveModal />
  <ColorModeModal />
  <ThemeModal />
  <LanguageModal />

  <input type="file" id="openfile" style="display:none">
</template>

<script>
/**
 * @module App
 * @description The Vue component for the entire web application.
 */
import CanvasContainer from './CanvasContainer.vue';
import WelcomeMessage from './WelcomeMessage.vue';
import Sidebar from './Sidebar.vue';
import StatusArea from './StatusArea.vue';
import Toolbar from './toolbar/Toolbar.vue';
import ObjBar from './ObjBar.vue';
import SimulatorControls from './SimulatorControls.vue';
import Footer from './Footer.vue';
import ModuleModal from './ModuleModal.vue';
import SaveModal from './SaveModal.vue';
import ColorModeModal from './ColorModeModal.vue';
import ThemeModal from './ThemeModal.vue';
import LanguageModal from './LanguageModal.vue';

// Import spatial computing styles
import '../styles/spatial-computing.css';

export default {
  components: {
    CanvasContainer,
    WelcomeMessage,
    Sidebar,
    StatusArea,
    Toolbar,
    ObjBar,
    SimulatorControls,
    Footer,
    ModuleModal,
    SaveModal,
    ColorModeModal,
    ThemeModal,
    LanguageModal
  }
}
</script>

<style>
/* ============================================
   Spatial Computing Theme - Global Styles
   ============================================ */

.popover-image {
  float:left;
  margin-right: 10px;
  margin-bottom: 4px;
  max-width: 245px;
}

.popover {
  padding-bottom:10px;
  background: rgba(30, 30, 35, 0.85) !important;
  backdrop-filter: blur(20px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(20px) saturate(180%) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4) !important;
  border-radius: 16px !important;
  color: rgba(255, 255, 255, 0.9) !important;
}

.popover .popover-header {
  background: rgba(255, 255, 255, 0.05) !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
  color: rgba(255, 255, 255, 0.9) !important;
  border-radius: 16px 16px 0 0 !important;
}

.popover .popover-arrow::before {
  border-color: rgba(255, 255, 255, 0.1) !important;
}

.popover .popover-arrow::after {
  border-color: rgba(30, 30, 35, 0.85) !important;
}

.popover-body {
  padding-bottom:0px;
  max-height: 80vh;
  overflow-y: auto;
  overflow-x: hidden;
  color: rgba(255, 255, 255, 0.85) !important;
}

.popover-body ul {
  padding-left: 10px;
}

#main-flex-wrapper {
  position: fixed;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

/* ============================================
   Bootstrap Modifications - Glassmorphism
   ============================================ */

.btn-group {
  padding-left: 0px;
  padding-right: 0px;
}

.btn-group>.dropdown:not(:last-child)>.btn {
  border-top-right-radius: 0;
  border-bottom-right-radius: 0;
}

.btn-group>.dropdown:nth-child(n+3)>.btn, .btn-group>:not(.btn-check)+.dropdown>.btn {
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
}

.dropdown-menu>li>.btn {
  border-radius: 0;
}

.dropdown-menu>li>div>ul>li>.btn {
  border-radius: 0;
}

.mobile-dropdown>ul {
  list-style-type: none;
  padding-left: 0;
}

.mobile-dropdown>ul>li>.btn {
  border-radius: 0;
}

.mobile-dropdown>ul>li>div>ul>li>.btn {
  border-radius: 0;
}

.modal-backdrop {
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1040;
  backdrop-filter: blur(5px);
}

/* ============================================
   Selected State - Apple-inspired Minimal
   ============================================ */

.btn.selected {
  background: rgba(255, 255, 255, 0.18) !important;
  color: white !important;
  box-shadow: 
    inset 0 1px 0 rgba(255, 255, 255, 0.25),
    0 0 0 1px rgba(255, 255, 255, 0.3),
    0 4px 12px rgba(0, 0, 0, 0.2) !important;
  border-color: rgba(255, 255, 255, 0.4) !important;
  backdrop-filter: blur(10px) saturate(150%);
  -webkit-backdrop-filter: blur(10px) saturate(150%);
  transform: translateY(-1px);
}

.btn.selected:hover {
  background: rgba(255, 255, 255, 0.25) !important;
  box-shadow: 
    inset 0 1px 0 rgba(255, 255, 255, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.4),
    0 6px 16px rgba(0, 0, 0, 0.25) !important;
}

.title {
  font-size: 10pt;
  color: rgba(255, 255, 255, 0.7);
}

/* ============================================
   Help Dropdown - Glass Style
   ============================================ */

#help-dropdown {
  width: 300px;
  background: rgba(30, 30, 35, 0.9);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  color: rgba(255, 255, 255, 0.9);
  max-height: 80vh;
  overflow: hidden;
  padding: 0;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4);
  border-radius: 16px;
}

#help-dropdown .help-dropdown-inner {
  display: flex;
  flex-direction: column;
  max-height: 80vh;
  padding-right: 0px;
}

#help-dropdown .help-popup-body-wrapper {
  flex: 1;
  min-height: 0;
}

#help-dropdown .help-popup-body {
  overflow-y: auto;
  height: 100%;
  padding: 0.5rem 0;
  padding-right: 0.5rem;
}

#help-dropdown .help-popup-footer {
  flex-shrink: 0;
  padding: 0 0 0.5rem;
}

#help-dropdown a {
  color: #a9ccff;
  text-decoration: none;
}

#help-dropdown a:hover {
  color: #bdd6f9;
  text-decoration: underline;
}

#welcome_instruction a:hover {
  color: #3685f9 !important;
  text-decoration: underline !important;
}

/* ============================================
   Scrollbar - Glass Style
   ============================================ */

::-webkit-scrollbar {
  background: none;
  width: 12px;
  height: 12px;
}

::-webkit-scrollbar-thumb {
   border: solid 0 rgba(0, 0, 0, 0);
   border-right-width: 4px;
   border-left-width: 4px;
   -webkit-border-radius: 6px 2px;
   -webkit-box-shadow:
   inset 0 0 0 0px rgba(255, 255, 255, 0.15),
   inset 0 0 0 4px rgba(255, 255, 255, 0.15);
}

::-webkit-scrollbar-track-piece {
   margin: 4px 0;
}

::-webkit-scrollbar-thumb:hover {
    border-right-width: 3px;
    border-left-width: 3px;
   -webkit-box-shadow:
     inset 0 0 0 0px rgba(255, 255, 255, 0.25),
     inset 0 0 0 4px rgba(255, 255, 255, 0.25);
}

::-webkit-scrollbar-corner {
   background: transparent;
}

.ace_scrollbar-h{
  margin: 0 2px
}

/* Custom syntax highlighting for math.js template expressions */
.ace_support.ace_function {
  color: #d4ffa9 !important;
}

/* Fix bracket highlighting visibility for GitHub Dark theme */
.ace-github-dark .ace_marker-layer .ace_bracket {
  border: 1px solid rgba(255, 255, 255, 0.5) !important;
  border-radius: 0 !important;
  background: none !important;
}

.ace-github-dark .ace_marker-layer .ace_bracket-start {
  border: 1px solid rgba(255, 255, 255, 0.5) !important;
  border-radius: 0 !important;
  background: none !important;
}

/* Fix variable name highlighting visibility for GitHub Dark theme */
.ace-github-dark .ace_marker-layer .ace_selected-word {
  border: none !important;
  border-radius: 0 !important;
  background: rgba(255, 255, 255, 0.2) !important;
}

/* ============================================
   Modal - Glassmorphism Style
   ============================================ */

.modal-content {
  background: rgba(30, 30, 35, 0.9) !important;
  backdrop-filter: blur(20px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(20px) saturate(180%) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 24px 64px rgba(0, 0, 0, 0.5) !important;
  border-radius: 24px !important;
  color: rgba(255, 255, 255, 0.9) !important;
}

.modal-header {
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
  border-radius: 24px 24px 0 0 !important;
}

.modal-footer {
  border-top: 1px solid rgba(255, 255, 255, 0.1) !important;
  border-radius: 0 0 24px 24px !important;
}

.modal-title {
  color: rgba(255, 255, 255, 0.9) !important;
}

.btn-close {
  filter: invert(1) grayscale(100%) brightness(200%);
  opacity: 0.7;
}

.btn-close:hover {
  opacity: 1;
}

/* ============================================
   Form Controls - Glass Style
   ============================================ */

.form-control, .form-select {
  background: rgba(0, 0, 0, 0.2) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  color: rgba(255, 255, 255, 0.9) !important;
  border-radius: 8px !important;
}

.form-control:focus, .form-select:focus {
  background: rgba(0, 0, 0, 0.3) !important;
  border-color: rgba(100, 200, 255, 0.4) !important;
  box-shadow: 0 0 0 3px rgba(100, 200, 255, 0.1) !important;
  color: rgba(255, 255, 255, 0.9) !important;
}

.form-control::placeholder {
  color: rgba(255, 255, 255, 0.4) !important;
}

.form-label {
  color: rgba(255, 255, 255, 0.7) !important;
}

/* ============================================
   Dropdown Menu - Glass Style
   ============================================ */

.dropdown-menu {
  background: rgba(30, 30, 35, 0.9) !important;
  backdrop-filter: blur(20px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(20px) saturate(180%) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1), 0 16px 48px rgba(0, 0, 0, 0.4) !important;
  border-radius: 12px !important;
  padding: 8px !important;
  color: rgba(255, 255, 255, 0.9) !important;
}

.dropdown-item {
  color: rgba(255, 255, 255, 0.85) !important;
  border-radius: 8px !important;
  padding: 8px 16px !important;
  transition: all 0.2s cubic-bezier(0.34, 1.56, 0.64, 1) !important;
}

.dropdown-item:hover {
  background: rgba(255, 255, 255, 0.1) !important;
  color: white !important;
}

.dropdown-item:focus {
  background: rgba(255, 255, 255, 0.15) !important;
  color: white !important;
}

.dropdown-divider {
  border-color: rgba(255, 255, 255, 0.1) !important;
  margin: 8px !important;
}

/* ============================================
   Buttons - Glass Style Override
   ============================================ */

.btn-primary {
  background: rgba(10, 88, 202, 0.7) !important;
  border-color: rgba(100, 200, 255, 0.3) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15) !important;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1) !important;
}

.btn-primary:hover {
  background: rgba(10, 88, 202, 0.85) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 0 20px rgba(100, 200, 255, 0.3) !important;
  transform: translateY(-1px);
}

.btn-secondary {
  background: rgba(255, 255, 255, 0.08) !important;
  border-color: rgba(255, 255, 255, 0.1) !important;
  color: rgba(255, 255, 255, 0.9) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.1) !important;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1) !important;
}

.btn-secondary:hover {
  background: rgba(255, 255, 255, 0.15) !important;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15), 0 0 16px rgba(255, 255, 255, 0.1) !important;
}

.btn-outline-secondary {
  border-color: rgba(255, 255, 255, 0.2) !important;
  color: rgba(255, 255, 255, 0.85) !important;
}

.btn-outline-secondary:hover {
  background: rgba(255, 255, 255, 0.1) !important;
  border-color: rgba(255, 255, 255, 0.3) !important;
  color: white !important;
}

.btn:disabled {
  opacity: 0.4 !important;
}

/* ============================================
   Loading State - Glass Style
   ============================================ */

#toolbar-loading {
  background: rgba(20, 20, 25, 0.8) !important;
  backdrop-filter: blur(10px) saturate(150%) !important;
  -webkit-backdrop-filter: blur(10px) saturate(150%) !important;
}

/* ============================================
   Range Slider - Glass Style
   ============================================ */

.form-range::-webkit-slider-thumb {
  background: rgba(255, 255, 255, 0.9) !important;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3) !important;
  transition: all 0.2s cubic-bezier(0.34, 1.56, 0.64, 1) !important;
}

.form-range::-webkit-slider-thumb:hover {
  transform: scale(1.2);
  box-shadow: 0 0 16px rgba(255, 255, 255, 0.4) !important;
}

.form-range::-moz-range-thumb {
  background: rgba(255, 255, 255, 0.9) !important;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3) !important;
}

.form-range::-webkit-slider-runnable-track {
  background: rgba(255, 255, 255, 0.1) !important;
}

.form-range::-moz-range-track {
  background: rgba(255, 255, 255, 0.1) !important;
}

/* ============================================
   Check/Radio - Glass Style
   ============================================ */

.form-check-input {
  background-color: rgba(255, 255, 255, 0.1) !important;
  border-color: rgba(255, 255, 255, 0.2) !important;
}

.form-check-input:checked {
  background-color: rgba(10, 88, 202, 0.8) !important;
  border-color: rgba(100, 200, 255, 0.4) !important;
  box-shadow: 0 0 10px rgba(100, 200, 255, 0.3) !important;
}

.form-switch .form-check-input {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='-4 -4 8 8'%3e%3ccircle r='3' fill='rgba(255,255,255,0.9)'/%3e%3c/svg%3e") !important;
}

/* ============================================
   Laser Bloom Effect Classes
   ============================================ */

.laser-bloom {
  filter: drop-shadow(0 0 8px currentColor) 
          drop-shadow(0 0 16px currentColor) 
          drop-shadow(0 0 32px currentColor) !important;
}

.laser-bloom-intense {
  filter: drop-shadow(0 0 12px currentColor) 
          drop-shadow(0 0 24px currentColor) 
          drop-shadow(0 0 48px currentColor)
          drop-shadow(0 0 64px currentColor) !important;
}
</style>
