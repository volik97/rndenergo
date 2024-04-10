<script setup lang="ts">

import Table from "~/src/components/Table.vue";

  const getDataFromLocalStorage = () => {

    return JSON.parse(localStorage.getItem('dataToPrint') as string)
  }
  const printData = getDataFromLocalStorage()._value
  onMounted(() => {
    window.print()})

</script>

<template>
  <table id="printable">
    <thead>
    <tr>
      <th>Street</th>
      <th>Number</th>
      <th>TP</th>
    </tr>
    </thead>
    <tbody class="">
    <tr class="" v-for="(item, index) in printData" :key="index">
      <td >{{ item.street }}</td>
      <td>{{ item.number }}</td>
      <td>{{ item.tp }}</td>
    </tr>
    </tbody>
  </table>
</template>

<style scoped>
table {
  height: fit-content;
  margin: 40px auto;
  width: 70%;
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
</style>