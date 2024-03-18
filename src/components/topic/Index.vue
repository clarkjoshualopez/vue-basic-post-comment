<script setup>
import TopicActionMenu from "./ActionMenu.vue";
import TopicReplyButton from "./ReplyButton.vue";

const props = defineProps({
  topic: Object,
});

const emit = defineEmits(["on-edit", "on-delete", "toggle-comment"]);

const handleClickEdit = () => {
  emit("on-edit");
};

const handleClickDelete = () => {
  emit("on-delete");
};

const handleToggleComment = () => {
  emit("toggle-comment");
};
</script>

<template>
  <article class="flex max-w-xl flex-col items-start justify-between">
    <div class="w-full flex justify-between">
      <div class="flex items-center gap-x-4">
        <time datetime="2020-03-16" class="text-gray-500">Mar 16, 2020</time>
        <span
          class="relative z-10 rounded-full bg-gray-50 px-3 py-1.5 font-medium text-gray-600 hover:bg-gray-100"
          >Marketing</span
        >
      </div>

      <div class="relative">
        <TopicActionMenu
          @on-edit="handleClickEdit"
          @on-delete="handleClickDelete"
        />
      </div>
    </div>
    <div class="group relative">
      <h3
        class="mt-3 text-lg font-semibold leading-6 text-gray-900 group-hover:text-gray-600"
      >
        <a href="#" class="text-4xl font-semibold">
          <span class="absolute inset-0"></span>
          {{ topic.name }}
        </a>
      </h3>
      <p class="mt-5 line-clamp-3 text-lg leading-6 text-gray-600">
        {{
          topic?.description
            ? topic?.description
            : "Illo sint voluptas. Error voluptates culpa eligendi. Hic vel totam vitae illo. Non aliquid explicabo necessitatibus unde. Sed exercitationem placeat consectetur nulla deserunt vel. Iusto corrupti dicta."
        }}
      </p>
    </div>
    <div class="flex items-center mt-4 space-x-4">
      <TopicReplyButton
        :comments-count="topic.comments.length"
        @toggle-comment="handleToggleComment"
      />
    </div>
  </article>
</template>

<style scoped></style>
