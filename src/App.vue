<script setup>
import { provide, ref } from 'vue'
import { RouterView } from 'vue-router'
let city = ref('kwara')
provide('city', city)
</script>

<template>
  <RouterView />
</template>

<style scoped></style>
