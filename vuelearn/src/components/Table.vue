<script setup lang="ts">
import type {PropType} from "vue";
import type {Street} from "~/src/types/street";

const props = defineProps({
  data: {
    required: true,
    type: Array as PropType<Street[]>,
  }
})
const streets = props.data.map(item => item)
console.log(streets === props.data)
const filterText = ref('')
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
const deleteRow = (index:number) => {
  streets.splice(index, 1)
}
// const appendItem = (araay) => {
//
// }
const filteredItems = computed(() => {
  let filtered = streets;
  // Filter by Street
  if (filters.value.street) {
    filtered = filtered.filter(item => item.street.toLowerCase().includes(filters.value.street.toLowerCase()));
  }
  // Filter by number
  if (numberFilterType.value === 'range' && rangeFilter.value.min && rangeFilter.value.max) {
    filtered = filtered.filter(item => parseInt(item.number) >= parseInt(rangeFilter.value.min) && parseInt(item.number) <= parseInt(rangeFilter.value.max));
  } else if (numberFilterType.value === 'exact' && exactNumberFilter.value) {
    filtered = filtered.filter(item => item.number === exactNumberFilter.value);
  }
  // Filter by TP
  if (filters.value.tp) {
    filtered = filtered.filter(item => item.tp.toLowerCase().includes(filters.value.tp.toLowerCase()));
  }
  return filtered;
});


</script>

<template>
  <div class="flex">
      <div class="flex items-end justify-stretch gapper">
        <div class="flex flex-col gapper">
          <label for='street'>Улица</label>
          <input id="street" type="text" class="input-field" v-model="filters.street" placeholder="Filter by Street...">
        </div>
        <div class="number-container flex flex-col gap-2">
          <div class="flex gap-4 items-center">
            <div class="flex gapper">
              <input type="radio" id="range" value="range" v-model="numberFilterType">
              <label for="range" class="ml-2">Range</label>
            </div>
            <div class="flex gapper">
              <input type="radio" id="exact" value="exact" v-model="numberFilterType">
              <label for="exact" class="ml-2">Exact</label>
            </div>
          </div>
          <div v-if="numberFilterType === 'range'" class="flex gapper">
            <input type="text" class="input-field" v-model="rangeFilter.min" placeholder="Min Number">
            <input type="text" class="input-field" v-model="rangeFilter.max" placeholder="Max Number">
          </div>
          <div v-else class="input-number">
            <input type="text" class="input-field" v-model="exactNumberFilter" placeholder="Exact Number">
          </div>
        </div>
        <div class="flex flex-col gapper">
          <label for='tp'>ТП</label>
          <input id='tp' type="text" v-model="filters.tp" placeholder="Filter by TP...">
        </div>
      </div>
  </div>

  <div class='table-container'>
    <table>
    <thead>
    <tr>
      <th>Street</th>
      <th>Number</th>
      <th>TP</th>
    </tr>
    </thead>
    <tbody class="">
    <tr class="!mt-10" v-for="(item, index) in filteredItems" :key="index" @click="deleteRow(index)">
      <td>{{ item.street }}</td>
      <td>{{ item.number }}</td>
      <td>{{ item.tp }}</td>
    </tr>
    </tbody>
  </table>
<!--  <table>-->
<!--    <thead>-->
<!--    <tr>-->
<!--      <th>Street</th>-->
<!--      <th>Number</th>-->
<!--      <th>TP</th>-->
<!--    </tr>-->
<!--    </thead>-->
<!--    <tbody class="">-->
<!--    <tr class="!mt-10" v-for="(item, index) in filteredItems" :key="index">-->
<!--      <td class="text-center">{{ item.street }}</td>-->
<!--      <td>{{ item.number }}</td>-->
<!--      <td>{{ item.tp }}</td>-->
<!--    </tr>-->
<!--    </tbody>-->
<!--  </table>-->
  </div>
</template>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}
.table-container {
  display: flex;
  width: 100%;
  justify-content: space-between;
  gap: 200px;
}
.gapper {
  gap: 10px;
}
.number-container {
}
.input-field {
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
  margin-top: 10px;
}
</style>
