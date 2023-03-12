<script setup>
import { defineProps } from "vue";

const props = defineProps(["posts"]);

const { VITE_BASE_PHOTO_URL } = import.meta.env;
</script>

<template>
  <div v-if="props.posts.length > 0" class="image-gallary-container">
    <!-- <img
      v-for="post in props.posts"
      :key="post.id"
      :src="`${VITE_BASE_PHOTO_URL}${post.url}`"
    /> -->
    <div
      v-for="post in props.posts"
      :key="post.id"
      class="image"
      :style="{
        backgroundImage: 'url(' + VITE_BASE_PHOTO_URL + post.url + ')',
      }"
    ></div>
  </div>
  <div v-else class="image-gallary-container">No posts loaded yet</div>
</template>

<style scoped>
.image-gallary-container {
  display: flex;
  justify-content: left;
  flex-wrap: wrap;
  gap: 10px;
}

.image {
  margin: 5px;
  width: 293px;
  height: 293px;
  background-size: cover;
}
</style>
