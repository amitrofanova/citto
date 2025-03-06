<script setup lang="ts">
import { ref, onMounted } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import BookModal from './BookModal.vue';
import Dialog from 'primevue/dialog';

defineProps({
  books: [],
});

// defineEmits(['loadMore']);

// const booksArray = ref([]);
const table = ref(null);

const isModalOpen = ref(false);
const modalData = ref();

const selectRow = (data) => {
  console.log('data', data);
  modalData.value = data;
  isModalOpen.value = true;
};
</script>

<template>
  <!-- <InputText name="search" type="text" placeholder="Поиск" fluid /> -->
  <!-- :virtualScrollerOptions="{ itemSize: 46, delay: 200, showLoader: true, loading: true, numToleratedItems: 10, lazy: true, onLazyLoad: loadBooksLazy }" -->
  <DataTable
    :value="books"
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
        <Button label="Открыть" @click="selectRow(data)" severity="secondary"></Button>
      </template>
    </Column>
  </DataTable>
  <Dialog v-model:visible="isModalOpen" modal :style="{ width: '25rem' }">
    <BookModal :data="modalData"></BookModal>
  </Dialog>
</template>
