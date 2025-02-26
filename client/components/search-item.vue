<script setup lang="ts">
import { AisHighlight } from "vue-instantsearch/vue3/es/index.js";
import { computed } from "vue";
import { useData } from "vitepress";

const { localePath } = useData();

const props = defineProps({
  item: Object,
  origin: String,
});

const linkHref = computed(
  () =>
    `${localePath.value}${props.item.link}`
);

const title = computed(() =>
  [props.item.lvl1, props.item.lvl2, props.item.lvl3, props.item.lvl4]
    .filter((n) => !!n)
    .map(n => n.trim())
    .join(" > ")
);
</script>

<template>
  <a :href="linkHref">
    <div class="search-item">
      <span class="item-type-icon">{{ item.anchorLink ? "＃" : "☰" }}</span>
      <div class="search-item-content">
        <h3 class="preview"><AisHighlight attribute="sentence" :hit="item" /></h3>
        <p v-if="item.anchorLink" class="chapter-header">{{ title }}</p>
      </div>
      <span class="jump-icon">↪</span>
    </div>
  </a>
</template>

<style scoped lang="scss">
.item-type-icon {
  font-family: none;
  align-self: center;
  padding: 0 1rem 0 0;
  font-size: x-large;
  width: 32px;
}

.jump-icon {
  font-family: none;
  align-self: center;
  font-size: x-large;
  margin-left: 0.5rem;
}

.search-item {
  padding: 0.75rem 1rem;
  margin: 8px 0 0 0;
  border: solid 1px;
  border-radius: 6px;
  display: flex;

  border-color: var(--vp-custom-block-details-border);
  color: var(--vp-custom-block-details-text);
  background-color: var(--vp-custom-block-details-bg);
}

.search-item p {
  margin: 0px;
  font-size: smaller;
  color: var(--c-text-light-3);
}

.search-item:hover {
  color: #fff;
  background: var(--vp-c-brand-dark);
}

.search-item:hover > p {
  color: #fff;
}

/* .dark .search-item > p {
  color: var(--c-text-light-2);
} */

.preview {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.search-item-content {
  width: 100%;
  overflow: hidden;
  line-height: 18px;
}

.chapter-header {
  font-weight: 600;
}
</style>
