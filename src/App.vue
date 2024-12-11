<script setup lang="ts">
import { ref } from 'vue';
import { SisenseContextProvider, ThemeProvider } from '@sisense/sdk-ui-vue';
import NLQ from './components/NLQ.vue';
import DataSourcePicker from './components/DataSourcePicker.vue';

const sisenseUrl = import.meta.env.VITE_SISENSE_URL;
const sisenseToken = import.meta.env.VITE_SISENSE_TOKEN;
const selectedDataSource = ref('');

</script>

<template>
  <main>
    <SisenseContextProvider :url="sisenseUrl" :token="sisenseToken">
      <ThemeProvider>
        <div class="container">
          <DataSourcePicker v-model="selectedDataSource" />
          <NLQ :selectedDataSource="selectedDataSource" />
        </div>
      </ThemeProvider>
    </SisenseContextProvider>
  </main>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem;
}
</style>
