<script setup>
import Container from "./Container.vue";
import Cards from "./Cards.vue";
import LogInMessage from "./LogInMessage.vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import Suggestions from "./Suggestions.vue";

const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);
</script>

<template>
  <Container>
    <div v-if="!loadingUser">
      <div class="wrapper" v-if="user">
        <Cards />
        <Suggestions />
      </div>
      <LogInMessage v-else />
    </div>
    <div v-else class="spinner">
      <ASpin />
    </div>
  </Container>
</template>

<style scoped>
.wrapper {
  display: flex;
  justify-content: space-evenly;
  margin-top: 20px;
}
.spinner {
  height: 90vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
