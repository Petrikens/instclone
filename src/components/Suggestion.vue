<template>
  <div class="suggestion">
    <div class="suggestion_avatarr">
      <AAvatar src="https://joeschmoe.io/api/v1/random" />
    </div>
    <div class="suggestion_username">{{ suggestion.username }}</div>
    <AButton v-if="!isFollow" type="link" @click="followUser()">Follow</AButton>
    <AButton v-else type="link" @click="unfollowUser()">Following</AButton>
  </div>
</template>

<script setup>
import { defineProps, ref } from "vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import { supabase } from "../supabase";

const props = defineProps(["suggestion"]);
const userStore = useUserStore();

const { user } = storeToRefs(userStore);

const isFollow = ref(false);

const followUser = async () => {
  try {
    await supabase.from("followers_following").insert({
      follower_id: user.value.id,
      following_id: props.suggestion.id,
    });

    isFollow.value = true;
  } catch (error) {
    console.log(error);
  }
};

const unfollowUser = async () => {
  try {
    await supabase
      .from("followers_following")
      .delete()
      .eq("follower_id", user.value.id)
      .eq("following_id", props.suggestion.id);

    isFollow.value = false;
  } catch (error) {
    console.log(error);
  }
};
</script>

<style scoped>
.suggestion {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 0;
}
.suggestion_username {
  font-weight: 600;
}
</style>
