<script setup lang="ts">
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Button from 'primevue/button';
import BookModal from './BookModal.vue';
import Dialog from 'primevue/dialog';

defineProps({
  books: [],
});

const table = ref(null);

const isModalOpen = ref(false);
const modalData = ref();

/**
 * Передаёт данные из строки таблицы в модальное окно
 * @param {object} data - данные, формирующие строку таблицы
 */
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
    <Column field="title" header="Название"></Column>
    <Column field="authors" header="Авторы"></Column>
    <Column field="categories" header="Сатегории"></Column>
    <Column field="publishedDate" header="Дата публикации"></Column>
    <Column class="w-24 !text-end">
      <template #body="{ data }">
        <Button label="Открыть" @click="selectRow(data)" severity="secondary"></Button>
      </template>
    </Column>
  </DataTable>
  <Dialog v-model:visible="isModalOpen" modal :style="{ width: '40rem' }">
    <BookModal :data="modalData"></BookModal>
  </Dialog>
</template>
