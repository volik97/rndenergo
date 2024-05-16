<script setup lang="ts">
import { ref } from 'vue';
import { FilterMatchMode, FilterOperator } from 'primevue/api';
import type { Street } from "../types/street";
import type {PropType} from "vue";
const props = defineProps({

  data: {
    required: true,
    type: Array as PropType<Street[]>,
  }
})
  const streets = ref(props.data);
  const selectedStreets = ref();
  const filters = ref();


const initFilters = () => {
  filters.value = {
    global: { value: null, matchMode: FilterMatchMode.CONTAINS },
    street: {
      operator: FilterOperator.AND,
      constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }],
    },
    tp: {
      operator: FilterOperator.AND,
      constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }],
    },
    number: {
      operator: FilterOperator.AND,
      constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }],
    },
  };
};

initFilters();

const clearFilter = () => {
  initFilters();
};

</script>

<template>
  <div class="card">
    <DataTable
        v-model:filters="filters"
        v-model:selection="selectedStreets"
        :metaKeySelection="true"
        :value="streets"
        selectionMode="multiple"
        paginator
        :rows="10"
        dataKey="id"
        filterDisplay="row"
        :globalFilterFields="['street', 'tp', 'number']"
    >
      <template #header>
        <div class="flex justify-start gap-10">
          <Button
              class="py-1 pr-4 pl-2 bg-white text-xl border rounded-3xl text-center w-[100px] border-cyan-800"
              type="button"
              icon="pi pi-filter-slash"
              label="Clear"
              outlined
              @click="clearFilter()"
          />
          <IconField class="" iconPosition="left">
            <InputIcon>
              <i class="pi pi-search" />
            </InputIcon>
            <InputText
                v-model="filters['global'].value"
                placeholder="Поиск"
            />
          </IconField>
        </div>
      </template>
      <template #empty> No customers found. </template>
      <Column selectionMode="multiple" headerStyle="width: 1rem"></Column>
      <Column field="street" header="Улица" sortable style="min-width: 14rem">
        <template #body="{ data }">
          {{ data.street }}
        </template>
        <template #filter="{ filterModel }">
          <InputText
              v-model="filterModel.value"
              type="text"
              class="p-column-filter"
              placeholder="Search by name"
          />
        </template>
      </Column><Column field="number" header="Дом" sortable style="min-width: 14rem">
        <template #body="{ data }">
          {{ data.number }}
        </template>
        <template #filter="{ filterModel }">
          <InputText
              v-model="filterModel.value"
              type="text"
              class="p-column-filter"
              placeholder="Search by name"
          />
        </template>
      </Column>
      <Column field="tp" header="ТП" sortable style="min-width: 14rem">
        <template #body="{ data }">
          {{ data.tp }}
        </template>
        <template #filter="{ filterModel }">
          <InputText
              v-model="filterModel.value"
              type="text"
              class="p-column-filter"
              placeholder="Search by name"
          />
        </template>
      </Column>
    </DataTable>
  </div>
</template>

<style scoped>

</style>