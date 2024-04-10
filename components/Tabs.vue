<template>
  <div>
    <span
      class="builder-tabs-wrap"
      :style="{
        display: 'flex',
        flexDirection: 'row',
        justifyContent: tabHeaderLayout,
        overflow: 'auto',
        WebkitOverflowScrolling: 'touch',
      }"
    >
      <span
        v-for="(item, index) in tabs"
        :key="index"
        class="builder-tab-wrap"
        :class="{ 'builder-tab-active': activeTab === index }" 
        :style="{...(activeTab === index && activeTabStyle) || undefined}"
        @click="handleClick(index)"
      >
        <Blocks
          :parent="props.builderBlock.id"
          :path="`component.options.tabs.${index}.label`"
          :blocks="item.label"
        />
      </span>
    </span>
    <!-- Display blocks for the active tab's content -->
    <div v-if="activeTabContent">
      <Blocks
        :parent="props.builderBlock.id"
        :path="`component.options.tabs.${activeTab}.content`"
        :blocks="activeTabContent"
      />
    </div>
  </div>
</template>
<script setup>
import { ref, computed } from 'vue';
import { Blocks } from '@builder.io/sdk-vue';
const props = defineProps({
  tabs: Array,
  builderBlock: Object,
  defaultActiveTab: Number,
  collapsible: Boolean,
  tabHeaderLayout: String,
  activeTabStyle: Object,
});
const activeTab = ref(props.defaultActiveTab ? props.defaultActiveTab - 1 : 0);
// Compute the active tab's content directly
const activeTabContent = computed(() => {
  // Ensure tabs exist and have content
  return props.tabs && props.tabs.length > activeTab.value
    ? props.tabs[activeTab.value].content
    : undefined;
});
function handleClick(index) {
  if (index === activeTab.value && props.collapsible) {
    activeTab.value = -1; // Collapse tab
  } else {
    activeTab.value = index; // Set active tab
  }
}
</script>
<style scoped>
/* Styles remain unchanged */
.builder-tabs-wrap {
  border-bottom: 1px solid #ccc;
  padding-left: 0;
}
.builder-tab-wrap {
  display: inline-block;
  list-style: none;
  margin-bottom: -1px;
  padding: 10px 15px;
  cursor: pointer;
  background-color: #f9f9f9;
  border: 1px solid #ccc;
  border-bottom: none;
  transition: background-color 0.2s;
}
.builder-tab-active {
  background-color: #fff;
  border-top: 2px solid #007BFF;
  font-weight: bold;
}
.builder-tab-wrap:hover {
  background-color: #e9e9e9;
}
.tab-content {
  border: 1px solid #ccc;
  padding: 15px;
  background-color: #fff; /* White background for the content */
}
</style>