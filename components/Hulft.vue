<script setup lang="ts">
defineProps<{
  tag: string;
}>();
</script>

<template>
  <component :is="tag ?? 'h2'" class="koho-font text-orange-500"
    >HULFT</component
  >
  <slot></slot>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=KoHo:wght@700&display=swap");
.koho-font {
  font-family: "KoHo", sans-serif;
}
</style>
