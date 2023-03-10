<template>
  <div class="suggestions">
    <div class="suggestions_header">
      <div class="suggestions_title">Suggestions for you</div>
      <AButton type="text">See all</AButton>
    </div>

    <div v-if="!isLoading">
      <Suggestion
        v-for="suggestion in suggestions"
        :key="suggestion.id"
        :suggestion="suggestion"
      />
    </div>
    <div v-else class="spinner">
      <ASpin />
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { supabase } from "../supabase";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import Suggestion from "./Suggestion.vue";

const suggestions = ref([]);
const userStore = useUserStore();
const { user: loggedInUser } = storeToRefs(userStore);
const isLoading = ref(true);

const fetchSuggestions = async () => {
  try {
    const { data: users } = await supabase.from("users").select();
    const { data: followings } = await supabase
      .from("followers_following")
      .select("following_id")
      .eq("follower_id", loggedInUser.value.id);

    const followingIds = followings.map((id) => id.following_id);

    followingIds.push(loggedInUser.value.id);

    suggestions.value = users.filter((user) => !followingIds.includes(user.id));
  } catch (error) {
    console.log(error);
  } finally {
    isLoading.value = false;
  }
};

onMounted(() => {
  fetchSuggestions();
});
</script>

<style scoped>
.suggestions {
  width: 350px;
}
.suggestions_header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.suggestions_title {
  margin-bottom: 10px;
  font-weight: 900;
}

.spinner {
  height: 10vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
