<script setup>
import { ref } from "vue";

import CommentActionMenu from "./ActionMenu.vue";
import CommentForm from "./Form.vue";

const props = defineProps({
  comment: Object,
  persons: Array,
  topic: Object,
  userGuid: String,
});

const isEdit = ref(false);

const emit = defineEmits(["on-edit", "on-delete", "on-submit-edit"]);

const getPersonName = (guid) => {
  const person = props.persons.find((p) => p.guid == guid);

  if (!person) {
    return `User ${props.userGuid}`;
  }

  return `${person.first} ${person.last}`;
};

const formatDateTime = (dateString) => {
  const options = {
    year: "numeric",
    month: "short",
    day: "numeric",
    hour: "numeric",
    minute: "numeric",
    hour12: true,
  };

  const formattedDate = new Date(dateString).toLocaleDateString(
    "en-US",
    options
  );
  return formattedDate;
};

const handleClickEdit = () => {
  isEdit.value = true;
  emit("on-edit");
};

const handleClickDelete = () => {
  emit("on-delete");
};

const handleSubmitEditComment = (data) => {
  emit("on-submit-edit", data);
  isEdit.value = false;
};
</script>

<template>
  <div class="p-6 text-base bg-white rounded-lg dark:bg-gray-900">
    <footer class="flex justify-between items-center mb-2">
      <div class="flex items-center">
        <p
          class="inline-flex items-center mr-3 text-sm text-gray-900 dark:text-white font-semibold"
        >
          <img
            class="mr-2 w-6 h-6 rounded-full"
            :src="`https://ui-avatars.com/api/?name=${getPersonName(
              comment.by
            )}&background=1f9bf4&color=ffff`"
            alt="Michael Gough"
          />{{ getPersonName(comment.by) }}
        </p>
        <p class="text-sm text-gray-600 dark:text-gray-400">
          <time pubdate datetime="2022-02-08" title="February 8th, 2022">{{
            formatDateTime(comment.date)
          }}</time>
        </p>
      </div>
      <div v-if="comment.by == userGuid" class="relative">
        <CommentActionMenu
          @on-edit="handleClickEdit"
          @on-delete="handleClickDelete"
        />
      </div>
    </footer>
    <p v-if="!isEdit" class="text-gray-500 dark:text-gray-400">
      {{ comment.comment }}
    </p>

    <CommentForm
      v-else
      :topic="topic"
      :comment="comment"
      @submitted="handleSubmitEditComment"
    />
  </div>
</template>

<style scoped></style>
