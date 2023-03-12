<script setup>
import { RouterLink, useRouter } from "vue-router";
import AuthModal from "./AuthModal.vue";
import { useUserStore } from "../stores/users";
import { ref } from "vue";
import { storeToRefs } from "pinia";
import { LogoutOutlined } from "@ant-design/icons-vue";
import { UserOutlined } from "@ant-design/icons-vue";
import { HomeOutlined } from "@ant-design/icons-vue";
import UploadPhotoModal from "./UploadPhotoModal.vue";

const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);
const router = useRouter();
const searchUsername = ref("");

const onSearch = () => {
  if (searchUsername.value) {
    router.push(`/profile/${searchUsername.value}`);
    searchUsername.value = "";
  }
};

const handleLogout = async () => {
  await userStore.handleLogout();
};

const goToUsersProfile = () => {
  router.push(`/profile/${user.value.username}`);
};

const goToHomePage = () => {
  router.push(`/`);
};
</script>

<template>
  <div class="nav-container">
    <RouterLink to="/">Instagram</RouterLink>
    <AButton class="nav-button" type="text" @click="goToHomePage">
      <template #icon><HomeOutlined /></template>
      Home</AButton
    >
    <AInputSearch
      class="search-input"
      v-model:value="searchUsername"
      placeholder="username..."
      style="width: 200px"
      @search="onSearch"
    />
    <div v-if="!loadingUser">
      <div class="button-content" v-if="!user">
        <AuthModal class="nav-button" :isLogin="false" />
        <AuthModal class="nav-button" :isLogin="true" />
      </div>
      <div class="button-content" v-else>
        <AButton class="nav-button" type="text" @click="goToUsersProfile">
          <template #icon><UserOutlined /></template>

          Profile</AButton
        >
        <UploadPhotoModal />
        <AButton class="nav-button" type="text" @click="handleLogout">
          <template #icon><LogoutOutlined /></template>
          Logout</AButton
        >
      </div>
    </div>
  </div>
</template>

<style scoped>
.nav-container {
  position: sticky;
  width: 15vw;
  height: 100vh;
  top: 0;
  left: 0;
  border-right: 1px solid #cccccc;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.nav-container a {
  margin-top: 20px;
  margin-bottom: 40px;
  color: black;
  font-size: 20px;
}

.nav-container .search-input {
  margin-bottom: 20px;
}

.button-content {
  display: flex;
  align-items: center;
  flex-direction: column;
}

.nav-button {
  margin-bottom: 20px;
  font-size: 16px;
}
</style>
