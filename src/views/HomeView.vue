<script setup lang="ts">
import { ref, onMounted } from 'vue';
import BooksTable from '../components/BooksTable.vue';

const books = ref();
const search = ref('');
const loading = ref(false);

const searchBooks = async () => {
  if (!search.value.trim()) return; // Не ищем, если запрос пустой

  loading.value = true;
  books.value = [];

  try {
    const apiKey = 'AIzaSyBHiXBcVyWeK5JYQ-sUEItvoAJQLvEmdTQ';
    const url = `https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(search.value)}&key=${apiKey}`;

    const response = await fetch(url);
    const data = await response.json();

    if (data.items) {
      books.value = makeArray(data.items); // Сохраняем найденные книги
      console.log(books.value);
    }
  } catch (error) {
    console.error('Ошибка при поиске книг:', error);
  } finally {
    loading.value = false;
  }
};

const makeArray = (data) => {
  return data?.map((item) => {
    return {
      publishedDate: item.volumeInfo.publishedDate,
      authors: item.volumeInfo.authors?.join(),
      title: item.volumeInfo.title,
      categories: item.volumeInfo.categories?.join(),
      id: item.id,
      description: item.volumeInfo.description || item.volumeInfo.subtitle,
    };
  });
};
</script>

<template>
  <div>
    <h1>Поиск книг в Google Books</h1>
    <form @submit.prevent="searchBooks">
      <input
        type="text"
        v-model="search"
        placeholder="Введите название книги"
        @keyup.enter="searchBooks"
      />
      <button type="submit">Искать</button>
    </form>

    <div v-if="loading">Загрузка...</div>
    <!-- <div v-else>
      <ul v-if="books.length">
        <li v-for="book in books" :key="book.id">
          <strong>{{ book.volumeInfo.title }}</strong> - {{ book.volumeInfo.authors?.join(', ') }}
        </li>
      </ul>
      <div v-else>Книги не найдены</div>
    </div> -->
  </div>
  <BooksTable :books="books"></BooksTable>
</template>
