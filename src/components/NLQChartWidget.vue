<script setup lang="ts">
import { ref, watch } from 'vue'
import { ChartWidget } from '@sisense/sdk-ui-vue'
import { widgetComposer } from '@sisense/sdk-ui/analytics-composer'
import { Filter, Data } from '@sisense/sdk-data'
import type { NlqResponseData } from '@sisense/sdk-ui/ai'
import { isChartWidgetProps } from '@sisense/sdk-ui'

import type { WidgetProps } from '@sisense/sdk-ui/props'

interface Props {
  nlqResponse: NlqResponseData
  filters?: Filter[]
  onDataReady?: (data: Data) => Data
}

const props = withDefaults(defineProps<Props>(), {
  filters: () => []
})

const chartWidgetProps = ref<WidgetProps | null>(null)

watch(
  () => [props.nlqResponse],
  () => {
    const w = widgetComposer.toWidgetProps(props.nlqResponse, {
      useCustomizedStyleOptions: true,
    })
    chartWidgetProps.value = w
  },
  { immediate: true }
)
</script>

<template>

    <ChartWidget
      v-bind="chartWidgetProps"
      :onDataReady="onDataReady"
    >
      <template #topSlot>
        <template v-if="isError">
          {{ t('ai.errors.unexpected') }}
        </template>
      </template>
    </ChartWidget>
</template>