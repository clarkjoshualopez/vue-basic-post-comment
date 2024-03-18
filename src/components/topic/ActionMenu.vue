<script setup>
import { ref } from "vue";

const isShowMenu = ref(false);

const emit = defineEmits(["on-edit", "on-delete"]);

const handleEdit = () => {
  emit("on-edit");
  isShowMenu.value = false;
};

const handleDelete = () => {
  emit("on-delete");
  isShowMenu.value = false;
};
</script>

<template>
  <button
    @click="isShowMenu = !isShowMenu"
    id="dropdownComment1Button"
    data-dropdown-toggle="dropdownComment1"
    class="inline-flex items-center p-2 text-sm font-medium text-center text-gray-500 dark:text-gray-400 bg-white rounded-lg hover:bg-gray-100 focus:ring-4 focus:outline-none focus:ring-gray-50 dark:bg-gray-900 dark:hover:bg-gray-700 dark:focus:ring-gray-600"
    type="button"
  >
    <svg
      class="w-5 h-5"
      aria-hidden="true"
      xmlns="http://www.w3.org/2000/svg"
      fill="currentColor"
      viewBox="0 0 16 3"
    >
      <path
        d="M2 0a1.5 1.5 0 1 1 0 3 1.5 1.5 0 0 1 0-3Zm6.041 0a1.5 1.5 0 1 1 0 3 1.5 1.5 0 0 1 0-3ZM14 0a1.5 1.5 0 1 1 0 3 1.5 1.5 0 0 1 0-3Z"
      />
    </svg>
    <span class="sr-only">Comment settings</span>
  </button>
  <!-- Dropdown menu -->
  <div
    id="dropdownComment1"
    v-if="isShowMenu"
    class="absolute z-10 w-36 bg-white rounded divide-y divide-gray-100 shadow dark:bg-gray-700 dark:divide-gray-600"
  >
    <ul
      class="py-1 text-sm text-gray-700 dark:text-gray-200"
      aria-labelledby="dropdownMenuIconHorizontalButton"
    >
      <li>
        <a
          @click="handleEdit"
          class="cursor-pointer block font-semibold py-2 px-4 hover:bg-gray-100 dark:hover:bg-gray-600 dark:hover:text-white"
          >Edit</a
        >
      </li>
      <li>
        <a
          @click="handleDelete"
          class="cursor-pointer block font-semibold py-2 px-4 text-red-500 hover:bg-gray-100 dark:hover:bg-gray-600 dark:hover:text-white"
          >Delete</a
        >
      </li>
    </ul>
  </div>
</template>

<style scoped></style>
