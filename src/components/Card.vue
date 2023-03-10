<script setup>
import { defineProps, onMounted, ref } from "vue";
import { supabase } from "../supabase";

const props = defineProps(["post"]);
const username = ref("");
const { VITE_BASE_PHOTO_URL } = import.meta.env;

const fetchUsername = async () => {
  const { data } = await supabase
    .from("users")
    .select()
    .eq("id", props.post.owner_id)
    .single();

  username.value = data.username;
};

onMounted(() => {
  fetchUsername();
});
</script>

<template>
  <ACard hoverable style="width: 403px" class="card">
    <template #cover>
      <img alt="example" :src="`${VITE_BASE_PHOTO_URL}${post.url}`" />
    </template>
    <ACardMeta>
      <template #title>{{ username }}</template>
      <template #avatar>
        <a-avatar src="https://joeschmoe.io/api/v1/random" />
      </template>
      <template #description>{{ post.caption }}</template>
    </ACardMeta>
  </ACard>
</template>

<style scoped>
.card {
  margin-bottom: 20px;
}
img {
  width: 403px;
  max-height: 504px;
  min-height: 200px;
}
</style>
