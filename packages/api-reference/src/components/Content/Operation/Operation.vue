<script setup lang="ts">
import type { TransformedOperation } from '@scalar/types/legacy'
import { computed } from 'vue'

import { ExampleRequest } from '../../../features/ExampleRequest'
import { useHttpClientStore } from '../../../stores'
import { Anchor } from '../../Anchor'
import { Badge } from '../../Badge'
import {
  Section,
  SectionColumn,
  SectionColumns,
  SectionContent,
  SectionHeader,
} from '../../Section'
import EndpointDetails from './EndpointDetails.vue'
import EndpointPath from './EndpointPath.vue'
import { PathResponses } from './PathResponses'
import TestRequestButton from './TestRequestButton.vue'

const props = defineProps<{
  id?: string
  operation: TransformedOperation
}>()

const { availableTargets } = useHttpClientStore()

const customRequestExamples = computed(() => {
  const keys = ['x-custom-examples', 'x-codeSamples', 'x-code-samples']

  for (const key of keys) {
    if (
      props.operation.information?.[
        key as 'x-custom-examples' | 'x-codeSamples' | 'x-code-samples'
      ]
    ) {
      return props.operation.information[
        key as 'x-custom-examples' | 'x-codeSamples' | 'x-code-samples'
      ]
    }
  }

  return null
})
</script>
<template>
  <Section
    :id="id"
    :label="operation.name">
    <SectionContent>
      <SectionColumns>
        <SectionColumn>
          <Badge v-if="operation.information?.deprecated"> Deprecated </Badge>
          <div :class="operation.information?.deprecated ? 'deprecated' : ''">
            <SectionHeader :level="3">
              <Anchor :id="id ?? ''">
                {{ operation.name }}
              </Anchor>
            </SectionHeader>
          </div>
          <EndpointDetails :operation="operation" />
        </SectionColumn>
        <SectionColumn>
          <div class="examples">
            <ExampleRequest
              v-if="availableTargets.length"
              :customExamples="customRequestExamples"
              :operation="operation">
              <template #header>
                <EndpointPath
                  class="example-path"
                  :deprecated="operation.information?.deprecated"
                  :path="operation.path" />
              </template>
              <template #footer>
                <TestRequestButton :operation="operation" />
              </template>
            </ExampleRequest>
            <PathResponses
              :operation="operation"
              style="margin-top: 12px" />
          </div>
        </SectionColumn>
      </SectionColumns>
    </SectionContent>
  </Section>
</template>

<style scoped>
.examples {
  position: sticky;
  top: calc(var(--refs-header-height) + 24px);
}
.deprecated * {
  text-decoration: line-through;
}
.example-path {
  color: var(--scalar-color-2);
  font-family: var(--scalar-font-code);
}
.example-path :deep(em) {
  color: var(--scalar-color-1);
}
</style>
