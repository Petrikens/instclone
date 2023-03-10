<template>
  <div class="observer" ref="root"></div>
</template>

<script setup>
import { ref, onMounted, defineEmits } from "vue";

const observer = ref(null);
const root = ref(null);

const emits = defineEmits(["intersect"]);

onMounted(() => {
  observer.value = new IntersectionObserver(([entry]) => {
    console.log("sdfsdfsdf");
    if (entry && entry.isIntersecting) {
      emits("intersect");
    }
  });

  observer.value.observe(root.value);
});
</script>

<style scoped>
.observer {
  width: 1px;
  height: 1px;
}
</style>
