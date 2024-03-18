<script setup>
import { ref } from "vue";

import LoadingIcon from "./LoadingIcon.vue";

const props = defineProps({
  canLoadMore: Boolean,
});

const isLoading = ref(false);

const emit = defineEmits(["on-load-more"]);

const handleLoadMoreTopic = () => {
  isLoading.value = true;
  emit("on-load-more");
};

const handleDoneLoading = () => {
  isLoading.value = false;
};

defineExpose({
  handleDoneLoading,
});
</script>

<template>
  <button
    @click="handleLoadMoreTopic"
    :disabled="!canLoadMore || isLoading"
    type="button"
    class="w-40 inline-flex justify-center text-white focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg px-5 py-2.5 text-center me-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800 inline-flex items-center"
    :class="
      canLoadMore
        ? 'bg-blue-700 hover:bg-blue-800'
        : 'cursor-not-allowed bg-blue-700/[0.5]'
    "
  >
    <span v-if="!isLoading"> Load More </span>
    <span v-else>
      <LoadingIcon />
      Loading...
    </span>
  </button>
</template>

<style scoped></style>
