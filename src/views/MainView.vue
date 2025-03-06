<script setup lang="ts">
import { ref } from 'vue';
import BooksTable from '../components/BooksTable.vue';
import TestTable from '../components/TestTable.vue';

const books = ref();

// const fetchBooks = async () => {
//   const response = await fetch(
//     'https://www.googleapis.com/books/v1/volumes?q=cat&startIndex=0&maxResults=5',
//   );
//   const result = await response.json();

//   books.value = result.items.map((item) => {
//     return {
//       publishedDate: item.volumeInfo.publishedDate,
//       authors: item.volumeInfo.authors?.join(),
//       title: item.volumeInfo.title,
//       categories: item.volumeInfo.categories?.join(),
//     };
//   });
// };

// fetchBooks();
</script>

<template>
  <BooksTable></BooksTable>
  <!-- <TestTable></TestTable> -->
</template>
