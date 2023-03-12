<script setup>
import { RouterView } from 'vue-router';
import TheHeader from './components/TheHeader.vue';
</script>

<template>
  <v-app>
    <TheHeader />
    <v-main class="">
      <v-container class="py-12">
        <RouterView />
      </v-container>
    </v-main>
  </v-app>
</template>
