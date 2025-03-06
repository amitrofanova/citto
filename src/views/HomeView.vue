<script setup lang="ts">
import { ref, onMounted } from 'vue';
import BooksTable from '../components/BooksTable.vue';

const books = ref([]);
const search = ref('');
const loading = ref(false);

const apiKey = 'AIzaSyBHiXBcVyWeK5JYQ-sUEItvoAJQLvEmdTQ';
const urlBase = `https://www.googleapis.com/books/v1/volumes`;
const currentIndex = ref(0);
const loadingMore = ref(false); // Состояние загрузки дополнительных книг
const itemsPerPage = 10; // Количество книг за один запрос
const hasMoreBooks = ref(false); // Есть ли ещё книги для загрузки

const searchBooks = async () => {
  if (!search.value.trim()) return; // Не ищем, если запрос пустой

  const url = `${urlBase}?q=${encodeURIComponent(search.value)}&key=${apiKey}`;

  loading.value = true;
  books.value = [];

  try {
    const response = await fetch(url);
    const data = await response.json();

    if (data.items) {
      books.value = makeArray(data.items); // Сохраняем найденные книги
      console.log(books.value);
      hasMoreBooks.value = data.totalItems > books.value.length; // Проверяем, есть ли ещё книги
    }
  } catch (error) {
    console.error('Ошибка при поиске книг:', error);
  } finally {
    loading.value = false;
  }
};

const loadMoreBooks = async () => {
  loadingMore.value = true;
  currentIndex.value += itemsPerPage; // Увеличиваем индекс для следующей порции книг

  try {
    const url = `https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(search.value)}&startIndex=${currentIndex.value}&maxResults=${itemsPerPage}&key=${apiKey}`;

    const response = await fetch(url);
    const data = await response.json();

    if (data.items) {
      const loadedBooks = makeArray(data.items);
      books.value = [...books.value, ...loadedBooks]; // Добавляем новые книги к существующим
      hasMoreBooks.value = data.totalItems > books.value.length; // Проверяем, есть ли ещё книги
    }
  } catch (error) {
    console.error('Ошибка при загрузке книг:', error);
  } finally {
    loadingMore.value = false;
  }
};

// const loadMoreBooks = async () => {
//   currentIndex.value += 10;
//   const params = new URLSearchParams(url.search);
//   console.log('params', params);
//   params.set('startIndex', currentIndex.value.toString());

//   const new_url = new URL(`${url.origin}${url.pathname}?${params}`);
//   console.log(new_url);

//   // const response = await fetch(new_url);
//   // const result = await response.json();
//   // const loadedBooks = makeArray(result.items);

//   // books.value = [...books.value, ...loadedBooks];
// };

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
  <button @click="currentIndex += 10">next</button>
  <div v-if="hasMoreBooks" class="load-more">
    <button @click="loadMoreBooks" :disabled="loadingMore">Загрузить ещё</button>
  </div>
</template>
