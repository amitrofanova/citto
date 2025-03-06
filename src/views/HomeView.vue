<script setup lang="ts">
import { ref } from 'vue';
import BooksTable from '../components/BooksTable.vue';
import { Form } from '@primevue/forms';
import Button from 'primevue/button';
import InputText from 'primevue/inputtext';

const books = ref([]);
const search = ref('');
const loading = ref(false);

const apiKey = 'AIzaSyBHiXBcVyWeK5JYQ-sUEItvoAJQLvEmdTQ';
const urlBase = `https://www.googleapis.com/books/v1/volumes`;
const currentIndex = ref(0);
const loadingMore = ref(false); // Состояние загрузки дополнительных книг
const ITEMS_PER_PAGE = 10; // Количество книг за один запрос
const hasMoreBooks = ref(false); // Есть ли ещё книги для загрузки
const showError = ref(false);
const errorText = '';

/**
 * Первоначальный поиск по книгам
 */
const searchBooks = async () => {
  showError.value = false;
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
    showError.value = true;
    errorText.value = error;
  } finally {
    loading.value = false;
  }
};

/**
 * Загрузка дополнительных книг
 */
const loadMoreBooks = async () => {
  showError.value = false;
  loadingMore.value = true;
  currentIndex.value += ITEMS_PER_PAGE; // Увеличиваем индекс для следующей порции книг

  try {
    const url = `${urlBase}?q=${encodeURIComponent(search.value)}&startIndex=${currentIndex.value}&maxResults=${ITEMS_PER_PAGE}&key=${apiKey}`;

    const response = await fetch(url);
    const data = await response.json();

    if (data.items) {
      const loadedBooks = makeArray(data.items);
      books.value = [...books.value, ...loadedBooks]; // Добавляем новые книги к существующим
      hasMoreBooks.value = data.totalItems > books.value.length; // Проверяем, есть ли ещё книги
    }
  } catch (error) {
    showError.value = true;
    errorText.value = error;
  } finally {
    loadingMore.value = false;
  }
};

/**
 * Преобразование данных, полученных от API, в массив, пригодный для отображения в таблице
 * @param {object} data - данные, полученные от API
 */
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
  <div class="grid place-items-center gap-12 p-40">
    <h1 class="text-2xl font-extrabold">Поиск книг в Google Books</h1>
    <Form @submit="searchBooks" class="flex gap-4">
      <InputText type="text" v-model="search" placeholder="Введите название книги" class="w-140" />
      <Button type="submit" label="Искать" class="max-w-fit" @click="searchBooks" />
    </Form>
    <div v-if="loading">Загрузка...</div>
    <div v-if="showError">Книги не найдены</div>
    <div v-else class="grid gap-12 w-6xl">
      <BooksTable v-if="books.length" :books="books" />
      <Button
        v-if="hasMoreBooks"
        :disabled="loadingMore"
        :loading="loadingMore"
        label="Загрузить ещё"
        severity="secondary"
        class="justify-self-end"
        @click="loadMoreBooks"
      />
    </div>
  </div>
</template>
