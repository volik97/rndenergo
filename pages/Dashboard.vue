<script setup lang="ts">
import tableJson from '../newRostData.json'
import Table from "~/src/components/Table.vue";
const props = defineProps({
  city: String,
})

</script>

<template>
  <div class="min-h-screen min-w-screen p-20 flex flex-col gap-10">
    <NuxtLink to="/" class="py-2 px-4 bg-white text-xl border rounded-3xl text-center w-fit border-cyan-800 transition-all duration-100 hover:bg-cyan-800 hover:text-white">Выбор города/поселка</NuxtLink>
    <Table :data="tableJson"/>
  </div>
</template>

<style scoped>

</style>