<script setup>
import { supabase } from "../supabase";
import Card from "./Card.vue";
import Observer from "./Observer.vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import { onMounted, ref } from "vue";

const userStore = useUserStore();
const { user } = storeToRefs(userStore);

const posts = ref([]);
const lastPostIndex = ref(2);
const ownerIds = ref([]);
const reachEnd = ref(false);
const isLoading = ref(true);

const fetchData = async () => {
  try {
    const { data: followings } = await supabase
      .from("followers_following")
      .select("following_id")
      .eq("follower_id", user.value.id);

    ownerIds.value = followings.map((id) => id.following_id);

    const { data } = await supabase
      .from("posts")
      .select()
      .in("owner_id", ownerIds.value)
      .range(0, lastPostIndex.value)
      .order("created_at", { ascending: false });

    posts.value = data;
  } catch (error) {
    console.log(error);
  } finally {
    isLoading.value = false;
  }
};

const fetchNextPosts = async () => {
  if (!reachEnd.value) {
    const { data } = await supabase
      .from("posts")
      .select()
      .in("owner_id", ownerIds.value)
      .range(lastPostIndex.value + 1, lastPostIndex.value + 3)
      .order("created_at", { ascending: false });

    posts.value = [...posts.value, ...data];

    lastPostIndex.value = lastPostIndex.value + 3;

    if (data.length === 0) {
      reachEnd.value = true;
    }
  }
};

onMounted(() => {
  fetchData();
});
</script>

<template>
  <div v-if="!isLoading">
    <div class="timeline-container" v-if="posts.length">
      <Card v-for="post in posts" :key="post.id" :post="post" />
      <Observer v-if="posts.length" @intersect="fetchNextPosts" />
    </div>
    <div class="timeline-container" v-else>
      <h2>Follow other users to see their posts</h2>
    </div>
  </div>
  <div v-else class="spinner">
    <ASpin />
  </div>
</template>

<style scoped>
.timeline-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px 0;
}
.spinner {
  height: 90vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
