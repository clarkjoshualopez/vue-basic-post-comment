<script setup>
import axios from "axios";
import { onMounted, ref, computed } from "vue";

import TopicModalForm from "@/components/topic/ModalForm.vue";
import TopicComponent from "@/components/topic/Index.vue";
import CommentComponent from "@/components/comment/Index.vue";
import CommentForm from "@/components/comment/Form.vue";
import LoadMoreButton from "@/components/LoadMoreButton.vue";
import LoadingPageIcon from "@/components/LoadingPageIcon.vue";

const topics = ref([]);
const persons = ref([]);
const userId = ref([]);
const modalForm = ref(null);
const selectedTopic = ref(null);
const isTopicEdit = ref(false);
const loadedTopicData = ref([]);
const itemsPerPage = 20;
const currentPage = ref(1);
const loadMore = ref(null);
const isLoadingPage = ref(false);

const canLoadMore = computed(
  () => loadedTopicData.value.length < topics.value.length
);

const getData = async () => {
  isLoadingPage.value = true;

  const response = await axios.get(
    "https://atillc.blob.core.windows.net/data-collector/icode/test-data/topics.json"
  );

  topics.value = response.data.topics;
  persons.value = response.data.persons;

  loadPaginatedTopic();
  setUserId();
  isLoadingPage.value = false;
};

const loadPaginatedTopic = () => {
  const startIndex = (currentPage.value - 1) * itemsPerPage;
  const endIndex = startIndex + itemsPerPage;
  loadedTopicData.value = topics.value.slice(0, endIndex);
};

const setUserId = () => {
  const uid = generateRandomId();
  userId.value = uid;

  persons.value.push({
    first: "John",
    last: "Doe",
    guid: uid,
    email: "john.doe@example.com",
  });
};

const handleSubmitTopic = (data) => {
  if (data.is_edit) {
    const findTopic = loadedTopicData.value.find((t) => t.guid == data.guid);

    if (findTopic) {
      findTopic.name = data.title;
      findTopic.description = data.description;
    }
  } else {
    const newTopic = {
      guid: generateRandomId(),
      name: data.title,
      description: data.description,
      comments: [],
    };

    topics.value.unshift(newTopic);
    loadedTopicData.value.unshift(newTopic);
  }

  isTopicEdit.value = false;
};

const handleEditTopic = (guid) => {
  const findTopic = loadedTopicData.value.find((t) => t.guid === guid);

  if (findTopic) {
    selectedTopic.value = findTopic;
    isTopicEdit.value = true;

    modalForm.value.handleShowModalForm(findTopic);
  }
};

const handleDeleteTopic = (guid) => {
  const indexToRemove = loadedTopicData.value.findIndex((t) => t.guid === guid);

  if (indexToRemove !== -1) {
    topics.value.splice(indexToRemove, 1);
    loadedTopicData.value.splice(indexToRemove, 1);
  }
};

const handleToggleCommentForm = (guid, isEdit = false) => {
  const findTopic = loadedTopicData.value.find((t) => t.guid === guid);

  if (findTopic) {
    if (isEdit) {
      findTopic.is_show_comment = false;
    } else {
      findTopic.is_show_all_comment = findTopic.is_show_all_comment
        ? !findTopic.is_show_all_comment
        : true;
      findTopic.is_show_comment = findTopic.is_show_comment
        ? !findTopic.is_show_comment
        : true;
    }
  }
};

const handleSubmitComment = (data) => {
  const indexToRemove = loadedTopicData.value.findIndex(
    (t) => t.guid === data.topic_guid
  );
  const now = new Date();

  if (indexToRemove !== -1) {
    if (data.is_edit) {
      const findComment = loadedTopicData.value[indexToRemove].comments.find(
        (t) => t.id == data.id
      );

      if (findComment) {
        findComment.comment = data.comment;
        loadedTopicData.value[indexToRemove].is_show_comment = true;
      }
    } else {
      loadedTopicData.value[indexToRemove].comments.push({
        comment: data.comment,
        date: now.toISOString(),
        by: userId.value,
        id: generateRandomId(5),
      });
    }
  }
};

const handleDeleteComment = (topicGuid, commentId) => {
  const indexToRemove = loadedTopicData.value.findIndex(
    (t) => t.guid === topicGuid
  );

  if (indexToRemove !== -1) {
    const indexComment = loadedTopicData.value[
      indexToRemove
    ].comments.findIndex((c) => c.id === commentId);

    loadedTopicData.value[indexToRemove].comments.splice(indexComment, 1);
  }
};

const handleLoadMoreTopics = () => {
  setTimeout(() => {
    currentPage.value += 1;

    loadPaginatedTopic();
    loadMore.value.handleDoneLoading();
  }, 800);
};

const generateRandomId = (length = 3) => {
  const characters =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
  let randomId = "";

  for (let i = 0; i < length; i++) {
    const randomIndex = Math.floor(Math.random() * characters.length);
    randomId += characters.charAt(randomIndex);
  }

  return randomId;
};

onMounted(() => {
  getData();
});
</script>

<template>
  <LoadingPageIcon v-if="isLoadingPage" />
  <div v-else class="flex justify-center w-full">
    <section class="bg-white dark:bg-gray-900 py-8 lg:py-16 antialiased">
      <div class="flex justify-end mb-4">
        <TopicModalForm
          ref="modalForm"
          :topic="selectedTopic"
          :is-edit="isTopicEdit"
          @submitted="handleSubmitTopic"
          @on-close="isTopicEdit = false"
        />
      </div>
      <div class="max-w-2xl mx-auto px-4">
        <div class="flex flex-col space-y-8">
          <div
            v-for="topic in loadedTopicData"
            :key="topic.guid"
            class="border-b border-slate-300 border-solid pb-8"
          >
            <TopicComponent
              :topic="topic"
              @on-edit="() => handleEditTopic(topic.guid)"
              @on-delete="() => handleDeleteTopic(topic.guid)"
              @toggle-comment="() => handleToggleCommentForm(topic.guid)"
            />

            <!-- comments -->
            <div v-if="topic.is_show_all_comment">
              <CommentComponent
                v-for="(comment, index) in topic.comments"
                :key="index"
                :persons="persons"
                :comment="comment"
                :userGuid="userId"
                :topic="topic"
                @on-edit="() => handleToggleCommentForm(topic.guid, true)"
                @on-submit-edit="handleSubmitComment"
                @on-delete="() => handleDeleteComment(topic.guid, comment.id)"
              />
              <CommentForm
                v-if="topic.is_show_comment"
                :topic="topic"
                @submitted="handleSubmitComment"
              />
            </div>
          </div>
          <div class="flex justify-center">
            <LoadMoreButton
              ref="loadMore"
              :can-load-more="canLoadMore"
              @on-load-more="handleLoadMoreTopics"
            />
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped></style>
