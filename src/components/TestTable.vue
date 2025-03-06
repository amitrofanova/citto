<template>
  hi
  <div class="card">
    <DataTable
      :value="virtualCars"
      scrollable
      scrollHeight="400px"
      tableStyle="min-width: 50rem"
      :virtualScrollerOptions="{
        lazy: true,
        onLazyLoad: loadCarsLazy,
        itemSize: 46,
        delay: 200,
        showLoader: true,
        loading: lazyLoading,
        numToleratedItems: 10,
      }"
    >
      <Column field="title" header="title" style="width: 20%"> </Column>
      <Column field="publishedDate" header="publishedDatein" style="width: 20%"> </Column>
      <Column field="authors" header="authors" style="width: 20%"> </Column>
      <Column field="categories" header="categories" style="width: 20%"> </Column>
    </DataTable>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

import DataTable from 'primevue/datatable';
import Column from 'primevue/column';

let url = new URL('https://www.googleapis.com/books/v1/volumes?q=cat');
const currentIndex = ref(0);
const booksArray = ref([]);

const makeArray = (data) => {
  return data?.map((item) => {
    return {
      publishedDate: item.volumeInfo.publishedDate,
      authors: item.volumeInfo.authors?.join(),
      title: item.volumeInfo.title,
      categories: item.volumeInfo.categories?.join(),
    };
  });
};

onMounted(async () => {
  const response = await fetch(url);
  const result = await response.json();
  booksArray.value = makeArray(result.items);
  console.log(result.items);
  console.log(booksArray.value);
});

const lazyLoading = ref(false);

const loadCarsLazy = (event) => {
  !lazyLoading.value && (lazyLoading.value = true);

  currentIndex.value += 10;
  const params = new URLSearchParams(url.search);

  params.set('startIndex', currentIndex.value.toString());
  const new_url = new URL(`${url.origin}${url.pathname}?${params}`);

  const loadedBooks = ref();
  fetch(new_url)
    .then((response) => response.json())
    .then((result) => (loadedBooks.value = result));
  console.log('loadedBooks', loadedBooks.value);
  booksArray.value = [...booksArray.value, ...makeArray(loadedBooks.value.items)];
  lazyLoading.value = false;
};
</script>
