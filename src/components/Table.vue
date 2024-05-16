<script setup lang="ts">

import type {PropType} from "vue";
import type {Street} from "~/src/types/street";

const props = defineProps({
  data: {
    required: true,
    type: Array as PropType<{ id: number; street: string; number: string; tp: string; }[]>,
  }
})
const streets = ref([...props.data]);
const currentStreets = ref<{ id: number; street: string; number: string; tp: string; }[]>([])
const numberFilterType = ref('range');
const exactNumberFilter = ref('');
const filters = ref({
  street: '',
  tp: ''
});
const rangeFilter = ref({
  min: '',
  max: ''
});

const deleteRow = (id:string, table:string) => {
  if(table === 'left'){
    const index = streets.value.findIndex(item => String(item.id) === String(id));
    currentStreets.value.push(streets.value[index])
    if (index !== -1) {
    saveToLocalStorage()
    streets.value.splice(index, 1);}
  }
  else{
    const index = currentStreets.value.findIndex(item => String(item.id) === String(id));
    streets.value.push(currentStreets.value[index])
    if (index !== -1) {
      currentStreets.value.splice(index, 1);}
      saveToLocalStorage()
      console.log(JSON.parse(localStorage.getItem('dataToPrint')as string)._value)
  }
}

const filteredItems = computed(() => {
  let filtered = streets.value;
  // Filter by Street
  if (filters.value.street) {
    filtered = filtered.filter(item => item.street.toLowerCase().includes(filters.value.street.toLowerCase()));
  }
  // Filter by number
  if (numberFilterType.value === 'range' && (rangeFilter.value.min || rangeFilter.value.max)) {
    if(rangeFilter.value.min && !rangeFilter.value.max){
      filtered = filtered.filter(item => parseInt(item.number) >= parseInt(rangeFilter.value.min));
    }
    if(!rangeFilter.value.min && rangeFilter.value.max){
      filtered = filtered.filter(item => parseInt(item.number) <= parseInt(rangeFilter.value.max));
    }
    else{
      filtered = filtered.filter(item => parseInt(item.number) >= parseInt(rangeFilter.value.min) && parseInt(item.number) <= parseInt(rangeFilter.value.max));
    }
  } else if (numberFilterType.value === 'exact' && exactNumberFilter.value) {
    const exactNumFilter = exactNumberFilter.value.toLowerCase();
    filtered = filtered.filter(item => {
      if((item.number.split('').length >= 3) && (item.number.split('').includes('-'))){
        const min = Number(item.number.split('-')[0])
        const max = Number(item.number.split('-')[1])
        console.log(min, max, item.number.split('-'))
        if((min <= Number(exactNumFilter)) && (Number(exactNumFilter) <= max)){
          return item
        }

      }
      const num = item.number.toLowerCase();
      const exactRegex = new RegExp(`^${exactNumFilter.replace('-', '\\-')}(\\D|$)`);
      return num.match(exactRegex);
    });

  }
  // Filter by TP
  if (filters.value.tp) {
    filtered = filtered.filter(item => item.tp.toLowerCase().includes(filters.value.tp.toLowerCase()));
  }
  return filtered;
});
const saveToLocalStorage = () => {
  localStorage.setItem('dataToPrint', JSON.stringify(currentStreets))
}
const clearRightTable = () => {
  currentStreets.value = []
  streets.value = [...props.data]
  localStorage.clear()
}
onMounted(() => {
  if(localStorage.getItem('dataToPrint')){
    currentStreets.value = JSON.parse(localStorage.getItem('dataToPrint')as string)._value
    streets.value = streets.value.filter(item => !currentStreets.value.some(currentItem => item.id === currentItem.id))
  }
})
</script>

<template>
  <div class="container-filters flex">
      <div class="flex items-end justify-stretch gapper">
        <div class="flex flex-col gapper">
          <label for='street'>Улица</label>
          <input id="street" type="text" class="input-field" v-model="filters.street" placeholder="Filter by Street...">
        </div>
        <div class="number-container flex flex-col gap-2">
          <div class="flex items-center gapper">
            <div class="flex gapper-2">
              <input type="radio" id="range" value="range" v-model="numberFilterType">
              <label for="range" class="ml-2">Диапазон</label>
            </div>
            <div class="flex gapper-2">
              <input type="radio" id="exact" value="exact" v-model="numberFilterType">
              <label for="exact" class="ml-2">Точный</label>
            </div>
          </div>
          <div v-if="numberFilterType === 'range'" class="input-number flex">
            <input type="text" class="input-field" v-model="rangeFilter.min" placeholder="Min Number">
            <input type="text" class="input-field" v-model="rangeFilter.max" placeholder="Max Number">
          </div>
          <div v-else class="input-number">
            <input type="text" class="input-field" v-model="exactNumberFilter" placeholder="Exact Number">
          </div>
        </div>
        <div class="flex flex-col gapper">
          <label for='tp'>ТП</label>
          <input id='tp' class="input-field" type="text" v-model="filters.tp" placeholder="Filter by TP...">
        </div>
      </div>
      <div class="flex gapper">
        <NuxtLink to="/print" @click="saveToLocalStorage" class="button px-4 py-2 bg-white text-xl border rounded-3xl text-center border-cyan-800">Печать</NuxtLink>
        <button class="clear bg-white text-xl border rounded-3xl text-center" @click="clearRightTable">Очистить правую таблицу</button>
      </div>
  </div>

  <div class='table-container'>
    <table>
    <thead>
    <tr>
      <th>Улица</th>
      <th>Номер</th>
      <th>ТП</th>
    </tr>
    </thead>
    <tbody class="">
    <tr class="!mt-10" v-for="({street, number, tp, id}, index) in filteredItems" :key="index" @dblclick="deleteRow(String(id), 'left')">
      <td>{{ street }}</td>
      <td>{{ number }}</td>
      <td>{{ tp }}</td>
    </tr>
    </tbody>
  </table>
  <table id="printable">
    <thead>
    <tr>
      <th>Улица</th>
      <th>Номер</th>
      <th>ТП</th>
    </tr>
    </thead>
    <tbody class="">
    <tr class="" v-for="({street, number, tp, id}, index) in currentStreets" :key="index" @dblclick="deleteRow(String(id), 'right')">
      <td >{{ street }}</td>
      <td>{{ number }}</td>
      <td>{{ tp }}</td>
    </tr>
    </tbody>
  </table>
  </div>
</template>

<style scoped>
table {
  height: fit-content;
  width: 100%;
  border-collapse: collapse;
}
tr:hover{
  cursor: pointer;
  background-color: rgba(21,94,117,0.1);
}
th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  color: white;
  background-color: rgba(21, 94, 117, 0.9);
}
.container-filters {
  align-items: end;
  justify-content: space-between;
}
.table-container {
  display: flex;
  width: 100%;
  justify-content: space-between;
  gap: 200px;
}
.gapper {
  gap: 15px;
}
.gapper-2 {
  gap: 5px;
}
.number-container {
}
button {
  padding: 8px 30px;
  height: fit-content;
}
a:hover{
  background-color: rgb(21 94 117);
  color: #fff;
  transition: background-color;
  transition-duration: 200ms;
}
button:hover{
  background-color: rgb(21 94 117);
  color: #fff;
  transition: background-color;
  transition-duration: 200ms;
}
button.clear{
  border-color: red;
}
button.clear:hover{
  background-color: rgba(255,0,0,1)
;
}
.input-field {
  max-width: 180px;
  height: fit-content;
}
input {
  border-width: 1px;
  border-style: solid;
  border-color: rgb(21 94 117);
  border-radius: 20px;
  padding: 8px 16px;
}

.input-number {
  display: flex;
  gap: 10px;
  margin-top: 15px;
}
</style>
