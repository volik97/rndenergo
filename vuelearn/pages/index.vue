<script setup lang="ts">

import Background from "~/src/components/Background.vue";
import AutoComplete from "primevue/autocomplete";

const cityes = ref(['City1', 'City2', 'City3'])
const selectedCity = ref('City1');
const filteredCityes = ref();
const search = (event: { query: string }) => {

    if (!event.query.trim().length) {
      filteredCityes.value = [...cityes.value];
    } else {
      filteredCityes.value = cityes.value.filter((city) => {
        return city.toLowerCase().startsWith(event.query.toLowerCase());
      });
    }
}
</script>

<template>
  <div class="min-w-screen flex items-center justify-center min-h-screen overflow-clip relative">
    <div class="z-0 absolute w-screen h-screen flex items-end justify-center">
      <Background :class="'scale-[7] bottom-0'" />
    </div>
    <div class="z-10 mb-40 flex-col gap-4 flex items-center">
      <AutoComplete class="py-2 px-4 bg-white text-xl border rounded-3xl text-center w-fit focus:border-none border-cyan-800" v-model="selectedCity" dropdown :suggestions="filteredCityes" @complete="search" />
      <NuxtLink :to="{name: 'Dashboard', query: {city: selectedCity}}" class="py-2 px-4 bg-white text-xl border rounded-3xl text-center w-[100px] border-cyan-800">Next</NuxtLink>
    </div>
  </div>
</template>

<style scoped>
</style>