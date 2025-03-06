<script setup lang="ts">
const props = defineProps({
  data: {},
});

const { title, publishDate, authors, description } = props.data;
</script>

<template>
  <h1>{{ title }}</h1>
  <p>{{ description }}</p>
</template>
