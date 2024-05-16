<script setup lang="ts">
const props = defineProps({
  class: String
})
</script>

<template>
  <NuxtImg src="./img/img.png" :class='props.class' />
</template>

<style scoped>

</style>