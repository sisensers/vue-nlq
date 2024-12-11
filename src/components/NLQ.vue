<script setup lang="ts">
import { ref, watch, computed } from 'vue';
import { useFetch } from '@sisense/sdk-ui-vue';
import NLQChartWidget from './NLQChartWidget.vue';
import DataSourcePicker from './DataSourcePicker.vue';

const props = defineProps<{
  selectedDataSource: string
}>();

const text = ref('');
const result = ref<any>(null);
const enabled = ref(false);
const inputRef = ref<HTMLInputElement | null>(null);
const requestOptions = ref(null);

watch(() => props.selectedDataSource, () => {
  if (enabled.value) {
    enabled.value = false;
    result.value = null;
  }
});

const { data, isLoading: _isLoading, error } = useFetch(
  computed(() => `api/v2/ai/nlq/query/${props.selectedDataSource}`),
  requestOptions,
  { enabled }
);

const isLoading = computed(() => _isLoading && enabled.value);

watch(data, (newData) => {
  if (newData) {
    result.value = newData;
    enabled.value = false;
    text.value = '';
    inputRef.value?.focus();
  }
});

const send = () => {
  if (text.value.trim()) {
    requestOptions.value = {
      method: "POST",
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        text: text.value,
        numOfExamples: 1,
        timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
        enableFollowup: true
      })
    };
    enabled.value = true;
  }
};
</script>

<template>
  <div class="container">
    <div class="input-group">
      <input
        ref="inputRef"
        v-model="text"
        type="text"
        :disabled="isLoading"
        placeholder="Ask a question about your data..."
        @keydown.enter.prevent="!isLoading && send()"
      />
      <button @click="send" :disabled="isLoading || !text.trim()">
        {{ isLoading ? 'Loading...' : 'Send' }}
      </button>
    </div>

    <div v-if="error" class="error">{{ error.message }}</div>
    <NLQChartWidget v-if="result" :nlqResponse="result" />
  </div>
</template>

<style scoped>
.container {
  background: white;
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  padding: 1.5rem;
}

.input-group {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

input {
  flex: 1;
  padding: 0.75rem;
  border: 1px solid var(--color-border);
  border-radius: 0.375rem;
}

button {
  padding: 0.75rem 1.5rem;
  background: var(--color-primary);
  color: white;
  border: none;
  border-radius: 0.375rem;
  min-width: 100px;
}

button:disabled {
  background: var(--color-disabled);
  cursor: not-allowed;
}

.error {
  color: var(--color-error);
  padding: 0.75rem;
  background: var(--color-error-bg);
  border-radius: 0.375rem;
}
</style>