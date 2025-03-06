<script setup lang="ts">
const props = defineProps({
  data: {},
});
const { title, publishedDate, authors, description } = props.data;
</script>

<template>
  <h1>{{ title }}</h1>
  <p>{{ description }}</p>
  <p>Дата публикации: {{ publishedDate }}</p>
  <p>Авторы: {{ authors }}</p>
</template>
