<script setup lang="ts">
import { ref, onMounted } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import BookModal from './BookModal.vue';
import Dialog from 'primevue/dialog';
// const props = defineProps({
//   books: [],
// });

// defineEmits(['loadMore']);

const books = ref();
const booksArray = ref([]);
let url = new URL('https://www.googleapis.com/books/v1/volumes?q=cat');
const currentIndex = ref(0);
const lazyLoading = ref(false);
const table = ref(null);

const fetchBooks = async () => {
  const response = await fetch(url);
  const result = await response.json();
  booksArray.value = makeArray(result.items);
};

fetchBooks();

const loadBooksLazy = async () => {
  !lazyLoading.value && (lazyLoading.value = true);

  currentIndex.value += 10;
  const params = new URLSearchParams(url.search);

  params.set('startIndex', currentIndex.value.toString());
  const new_url = new URL(`${url.origin}${url.pathname}?${params}`);

  // fetch(new_url)
  //   .then((response) => response.json())
  //   .then((result) => (loadedBooks.value = makeArray(result.items)));
  const response = await fetch(new_url);
  const result = await response.json();
  const loadedBooks = makeArray(result.items);

  booksArray.value = [...booksArray.value, ...loadedBooks];
  lazyLoading.value = false;
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

onMounted(() => {
  console.log(table.value.scrollTop);
});

const isModalOpen = ref(false);
const modalData = ref();

const selectRow = (data) => {
  console.log('data', data);
  modalData.value = data;
  isModalOpen.value = true;
};
</script>

<template>
  <!-- :virtualScrollerOptions="{ itemSize: 46, delay: 200, showLoader: true, loading: true, numToleratedItems: 10, lazy: true, onLazyLoad: loadBooksLazy }" -->
  <DataTable
    :value="booksArray"
    scrollable
    scrollHeight="400px"
    tableStyle="min-width: 50rem"
    ref="table"
  >
    <Column field="title" header="Title"></Column>
    <Column field="authors" header="Authors"></Column>
    <Column field="categories" header="Categories"></Column>
    <Column field="publishedDate" header="publishedDate"></Column>
    <Column class="w-24 !text-end">
      <template #body="{ data }">
        <Button icon="pi pi-search" @click="selectRow(data)" severity="secondary" rounded></Button>
      </template>
    </Column>
  </DataTable>
  <button @click="loadBooksLazy()">next</button>
  <Dialog v-model:visible="isModalOpen" modal :style="{ width: '25rem', backgroundColor: '#fff' }">
    <BookModal :data="modalData"></BookModal>
  </Dialog>
</template>
