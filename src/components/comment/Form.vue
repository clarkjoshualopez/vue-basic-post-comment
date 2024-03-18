<script setup>
import { onMounted, ref, reactive } from "vue";

import LoadingIcon from "@/components/LoadingIcon.vue";

const props = defineProps({
  topic: Object,
  comment: {
    type: Object,
    value: null,
  },
});

const isSubmitBtnLoading = ref(false);
const form = reactive({
  comment: "",
  topic_guid: props.topic.guid,
  id: "",
  is_edit: false,
});

const emit = defineEmits(["submitted"]);

const handleSubmit = () => {
  isSubmitBtnLoading.value = true;

  setTimeout(() => {
    isSubmitBtnLoading.value = false;
    emit("submitted", form);
    form.comment = "";
    form.is_edit = false;
    form.id = "";
  }, 800);
};

onMounted(() => {
  if (props.comment) {
    form.comment = props.comment.comment;
    form.id = props.comment.id;
    form.is_edit = true;
  }
});
</script>

<template>
  <form class="p-6 mb-6" @submit.prevent="handleSubmit">
    <div
      class="py-2 px-4 mb-4 bg-white rounded-lg rounded-t-lg border border-gray-200 dark:bg-gray-800 dark:border-gray-700"
    >
      <label for="comment" class="sr-only">Your comment</label>
      <textarea
        id="comment"
        rows="4"
        v-model="form.comment"
        class="px-0 w-full text-sm text-gray-900 border-0 focus:ring-0 focus:outline-none dark:text-white dark:placeholder-gray-400 dark:bg-gray-800"
        placeholder="Write a comment..."
        required
      ></textarea>
    </div>
    <button
      :disabled="isSubmitBtnLoading"
      type="submit"
      :class="
        !form.comment && isSubmitBtnLoading
          ? 'bg-blue-700/[0.5] cursor-not-allowed'
          : 'bg-blue-700'
      "
      class="inline-flex items-center py-2.5 px-4 text-xs font-medium text-center text-white rounded-lg focus:ring-4 focus:ring-blue-200 dark:focus:ring-blue-900 hover:bg-blue-800"
    >
      <LoadingIcon v-if="isSubmitBtnLoading" />
      Comment
    </button>
  </form>
</template>

<style scoped></style>
