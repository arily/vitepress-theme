<script lang="ts" setup>
import { nextTick, ref, watchPostEffect } from 'vue'
// @ts-ignore
import { useSidebar } from '@theme-default/composables/sidebar.js'
import VPSidebarItem from '@theme-default/components/VPSidebarItem.vue'
const { sidebar, hasSidebar, sidebarGroups } = useSidebar()
const props = defineProps<{
  open: boolean
}>()
// a11y: focus Nav element when menu has opened
const navEl = ref<{ wrapRef: HTMLElement } | null>(null)
watchPostEffect(async () => {
  if (props.open) {
    await nextTick()
    navEl.value?.wrapRef.focus()
  }
})
</script>

<template>
  <ElScrollbar
    tag="aside"
    v-if="hasSidebar"
    class="vp-sidebar"
    view-class="vp-sidebar-view"
    :class="{ open }"
    ref="navEl"
    @click.stop
  >
    <div class="curtain" />

    <nav class="vp-sidebar-nav">
      <slot name="sidebar-nav-before" />

      <div v-for="item in sidebarGroups" :key="item.text" class="group">
        <VPSidebarItem :item="item" :depth="0" />
      </div>

      <slot name="sidebar-nav-after" />
    </nav>
  </ElScrollbar>
</template>

<style lang="scss">
.vp-sidebar {
  position: fixed !important;
  padding: 0 32px;
  top: var(--vp-layout-top-height, 0px);
  bottom: 0;
  left: 0;
  z-index: var(--vp-z-index-sidebar);
  width: calc(100vw - 64px);
  max-width: 320px;
  background-color: var(--vp-c-bg);
  opacity: 0;
  box-shadow: var(--vp-c-shadow-3);
  overflow-x: hidden;
  overflow-y: auto;
  transform: translateX(-100%);
  transition: opacity 0.5s, transform 0.25s ease;
}
.vp-sidebar-view {
  padding: 32px 0 64px;
  overflow-x: hidden;
}
.vp-sidebar.open {
  opacity: 1;
  visibility: visible;
  transform: translateX(0);
  transition: opacity 0.25s,
              transform 0.5s cubic-bezier(0.19, 1, 0.22, 1);
}
.dark .vp-sidebar {
  box-shadow: var(--vp-shadow-1);
}
@media (min-width: 960px) {
  .vp-sidebar {
    z-index: 1;
    width: var(--vp-sidebar-width);
    max-width: 100%;
    background-color: var(--vp-c-bg-alt);
    opacity: 1;
    visibility: visible;
    box-shadow: none;
    transform: translateX(0);
  }
  .vp-sidebar-view {
    padding-top: var(--vp-nav-height);
    padding-bottom: 32px;
  }
}
@media (min-width: 1440px) {
  .vp-sidebar {
    padding-left: max(32px, calc((100% - (var(--vp-layout-max-width) - 64px)) / 2));
    width: calc((100% - (var(--vp-layout-max-width) - 64px)) / 2 + var(--vp-sidebar-width) - 32px);
  }
}
@media (min-width: 960px) {
  .curtain {
    /* position: sticky; */
    /* top: -64px; */
    // left: 0;
    // z-index: 1;
    margin-top: calc(var(--vp-nav-height) * -1);
    /* margin-right: -32px; */
    /* margin-left: -32px; */
    height: var(--vp-nav-height);
    /* background-color: var(--vp-sidebar-bg-color); */
  }
  .el-scrollbar .vp-sidebar {
    height: calc(100% - 64px);
    top: 64px;
  }
}
.vp-sidebar-nav {
  outline: 0;
}

.vp-sidebar-nav > .group:not(:first-child) {
  margin-top: 16px;
}

.group + .group {
  border-top: 1px solid var(--vp-c-divider);
  padding-top: 16px;
}
@media (min-width: 960px) {
  .group {
    padding-top: 16px;
    width: calc(var(--vp-sidebar-width) - 64px);
  }
}

.group .VPSidebarItem.level-0 {
  padding-bottom: 0;
  > .item > .text {
    padding: 0;
    margin: 4px 0;
  }
}

</style>
