<script setup>
import { RouterLink } from 'vue-router';
</script>

<template>
  <v-app-bar density="compact" color="blue-grey-lighten-5" elevation="2">
    <v-app-bar-title
      class="text-indigo-darken-4 font-weight-medium"
    >
      GPT챗붓
    </v-app-bar-title>
    <!-- <span class="mx-4">
      <RouterLink :to="{ name: 'home' }" active-class="font-weight-medium text-indigo-darken-4">
        하나하나묻기
      </RouterLink>
    </span> -->
    <!-- <span class="mx-4">
      <RouterLink :to="{ name: 'auto' }" active-class="font-weight-medium text-indigo-darken-4">
        질문하나 던져노고 알아서 답하기
      </RouterLink>
    </span> -->
    <span class="mx-4">
      <a href="https://github.com/visionboy/VueChatGPT" target="_blank" rel="noopener noreferrer">
        <v-icon size="x-large">
          mdi-github
        </v-icon>
      </a>
    </span>
  </v-app-bar>
</template>
