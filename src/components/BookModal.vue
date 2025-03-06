<script setup lang="ts">
const props = defineProps({
  data: {},
});
const { title, publishedDate, authors, description } = props.data;
</script>

<template>
  <div class="grid gap-4">
    <h1 class="text-xl font-bold">{{ title }}</h1>
    <p>{{ description }}</p>
    <p>Дата публикации: {{ publishedDate }}</p>
    <p>Авторы: {{ authors }}</p>
  </div>
</template>
