<script setup lang="ts">
import { ref, watch } from 'vue';
import { useFetch } from '@sisense/sdk-ui-vue';

const selectedDataSource = ref('');
const enabled = ref(true);

const { data, isLoading, error } = useFetch(
  'api/v2/datamodels/schema',
  null,
  { enabled }
);

const emit = defineEmits(['update:modelValue']);

watch(selectedDataSource, (newValue) => {
  emit('update:modelValue', newValue);
});
</script>

<template>
  <div class="picker-container">
    <select
      v-model="selectedDataSource"
      :disabled="isLoading"
      class="data-source-select"
    >
      <option value="" disabled>Select a data source</option>
      <option
        v-for="model in data"
        :key="model.title"
        :value="model.title"
      >
        {{ model.title }}
      </option>
    </select>

    <div v-if="error" class="error">{{ error.message }}</div>
  </div>
</template>

<style scoped>
.picker-container {
  margin-bottom: 1rem;
}

.data-source-select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid var(--color-border);
  border-radius: 0.375rem;
  background: white;
  appearance: none;
  background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
  background-position: right 1rem center;
  background-size: 1em;
  background-repeat: no-repeat;
}

.error {
  color: var(--color-error);
  padding: 0.75rem;
  background: var(--color-error-bg);
  border-radius: 0.375rem;
  margin-top: 0.5rem;
}
</style>
