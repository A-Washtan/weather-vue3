<script setup>
import { RouterLink, RouterView } from 'vue-router'
import SiteNavigation from './components/SiteNavigation.vue';
import Footer from './components/Footer.vue';

</script>

<template>
<div class="flex flex-col min-h-screen font-Cairo bg-w-primary">
  <SiteNavigation />
  <RouterView />
</div>
<Footer />
</template>

<style scoped>

</style>
