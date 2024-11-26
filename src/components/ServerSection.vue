<script setup lang="ts">
import CampaignCard from './CampaignCard.vue';

const props = defineProps({
  title: String,
  data: Object,
  server: String,
})

</script>

<template>
  <h1>{{ title }}</h1>
  <div v-if="data && data.length" class="d-flex flex-wrap justify-center pa-2 pa-md-4">
    <CampaignCard v-for="item in data" :item :server />
  </div>
</template>

<style lang="css" scoped>

</style>