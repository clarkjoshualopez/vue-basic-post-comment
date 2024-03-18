<script setup>
import { onMounted, ref, reactive } from "vue";

import LoadingIcon from "@/components/LoadingIcon.vue";

const props = defineProps({
  isEdit: {
    type: Boolean,
    value: false,
  },
});

const isShow = ref(false);
const isSubmitBtnLoading = ref(false);
const form = reactive({
  guid: "",
  title: "",
  description: "",
  is_edit: false,
});

const emit = defineEmits(["submitted", "on-close"]);

const handleSubmit = () => {
  isSubmitBtnLoading.value = true;

  setTimeout(() => {
    isSubmitBtnLoading.value = false;
    isShow.value = false;

    emit("submitted", form);
    clearForm();
  }, 800);
};

const handleCancel = () => {
  isShow.value = false;

  clearForm();
  emit("on-close");
};

const clearForm = () => {
  form.title = "";
  form.description = "";
  form.guid = "";
  form.is_edit = false;
};

const handleShowModalForm = (topic) => {
  form.guid = topic.guid;
  form.title = topic.name;
  form.description = topic?.description ?? "";
  form.is_edit = true;

  isShow.value = true;
};

defineExpose({
  handleShowModalForm,
});

onMounted(() => {});
</script>

<template>
  <button
    @click="isShow = true"
    class="inline-flex items-center py-2.5 px-4 font-medium text-center text-white bg-blue-700 rounded-lg focus:ring-4 focus:ring-blue-200 dark:focus:ring-blue-900 hover:bg-blue-800"
  >
    Add Post
  </button>
  <div
    v-if="isShow"
    class="relative z-20"
    aria-labelledby="modal-title"
    role="dialog"
    aria-modal="true"
  >
    <div
      class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
    ></div>

    <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
      <div
        class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0"
      >
        <form
          @submit.prevent="handleSubmit"
          class="relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
        >
          <div class="bg-white px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
            <div class="space-y-12">
              <div class="border-b border-gray-900/10 pb-12">
                <h2 class="text-xl font-semibold leading-7 text-gray-900">
                  {{ isEdit ? "Edit" : "Create" }} Topic
                </h2>

                <div
                  class="mt-10 grid grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6"
                >
                  <div class="sm:col-span-full">
                    <label
                      for="title"
                      class="block text-sm font-medium leading-6 text-gray-900"
                      >Title</label
                    >
                    <div class="mt-2">
                      <input
                        type="text"
                        name="title"
                        id="title"
                        autocomplete="given-name"
                        v-model="form.title"
                        class="block px-2 w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                      />
                    </div>
                  </div>

                  <div class="col-span-full">
                    <label
                      for="description"
                      class="block text-sm font-medium leading-6 text-gray-900"
                      >Description</label
                    >
                    <div class="mt-2">
                      <textarea
                        id="description"
                        name="description"
                        rows="3"
                        v-model="form.description"
                        class="block px-2 w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                      ></textarea>
                    </div>
                    <p class="text-xs italic leading-6 text-gray-600">
                      Write a few sentences about your topic.
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="bg-gray-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
            <button
              type="submit"
              class="inline-flex w-full justify-center rounded-md px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 sm:ml-3 sm:w-auto"
              :disabled="isSubmitBtnLoading"
              :class="
                !form.title && isSubmitBtnLoading
                  ? 'bg-indigo-600/[0.5] cursor-not-allowed'
                  : 'bg-indigo-600'
              "
            >
              <LoadingIcon v-if="isSubmitBtnLoading" />
              Submit
            </button>
            <button
              @click="handleCancel"
              type="button"
              class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
