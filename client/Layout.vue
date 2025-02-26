<template>
  <div class="Layout" :class="layoutClass">
    <VPSkipLink />
    <VPBackdrop class="backdrop" :show="isSidebarOpen" @click="closeSidebar" />
    <VPNav>
      <template #nav-bar-title-before></template>
      <template #nav-bar-title-after></template>
      <template #nav-bar-content-before></template>
      <template #nav-bar-content-after></template>
      <template #nav-screen-content-before></template>
      <template #nav-screen-content-after></template>
    </VPNav>
    <VPLocalNav :open="isSidebarOpen" @open-menu="openSidebar" />
    <VPSidebar :open="isSidebarOpen">
      <template #sidebar-nav-before><slot name="sidebar-nav-before"></slot></template>
      <template #sidebar-nav-after><slot name="sidebar-nav-after"></slot></template>
    </VPSidebar>

    <div class="VPContent" id="VPContent" :class="contentClass">
      <NotFound v-if="route.component === NotFound" />
      <component v-else :is="component">
        <template #doc-footer-before></template>
        <template #doc-before></template>
        <template #doc-after></template>

        <template #aside-top></template>
        <template #aside-bottom></template>
        <template #aside-outline-before></template>
        <template #aside-outline-after></template>
        <template #aside-ads-before></template>
        <template #aside-ads-after></template>
      </component>
    </div>

    <VPFooter />
  </div>
</template>

<script setup lang="ts">

import { computed, inject, provide, watch } from 'vue'
import { useData, useRoute } from 'vitepress'
// @ts-ignore
import { useSidebar, useCloseSidebarOnEscape } from '@theme-default/composables/sidebar.js'
import VPSkipLink from '@theme-default/components/VPSkipLink.vue'
import VPBackdrop from '@theme-default/components/VPBackdrop.vue'
import VPNav from '@theme-default/components/VPNav.vue'
import VPLocalNav from '@theme-default/components/VPLocalNav.vue'
import VPFooter from '@theme-default/components/VPFooter.vue'
import VPSidebar from './components/sidebar.vue'
import { ThemeConfig } from '.'

const {
  isOpen: isSidebarOpen,
  open: openSidebar,
  close: closeSidebar,
  hasSidebar,
} = useSidebar()

const route = useRoute()
watch(() => route.path, closeSidebar)

useCloseSidebarOnEscape(isSidebarOpen, closeSidebar)

provide('close-sidebar', closeSidebar)
provide('is-sidebar-open', isSidebarOpen)

const { frontmatter } = useData()

const config = inject(ThemeConfig)

const NotFound = inject('NotFound')
const component = computed(() => {
  return config.layouts[frontmatter.value.layout] || config.layouts.default
})

const contentClass = computed(() => ({
  'has-sidebar': hasSidebar.value,
}))

const layoutClass = computed(() => ({
  [`layout-${frontmatter.value.layout}`]: frontmatter.value.layout,
}))

</script>

<style scoped>

.Layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.VPContent {
  flex-grow: 1;
  flex-shrink: 0;
  margin: var(--vp-layout-top-height, 0px) auto 0;
  width: 100%;
}

.VPContent.has-sidebar {
  margin: 0;
}

@media (min-width: 960px) {
  .VPContent {
    padding-top: var(--vp-nav-height);
  }

  .VPContent.has-sidebar {
    margin: var(--vp-layout-top-height, 0px) 0 0;
    padding-left: var(--vp-sidebar-width);
  }
}

@media (min-width: 1440px) {
  .VPContent.has-sidebar {
    padding-right: calc((100vw - var(--vp-layout-max-width)) / 2);
    padding-left: calc((100vw - var(--vp-layout-max-width)) / 2 + var(--vp-sidebar-width));
  }
}

</style>
