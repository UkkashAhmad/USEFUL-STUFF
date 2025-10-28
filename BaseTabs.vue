<script setup lang="ts">
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs'
import type { Component } from 'vue'

interface TabItem {
  label: string
  value: string
  component?: Component
  count?: number | string   // 👈 add count here
}

const props = defineProps<{
  tabs: TabItem[]
}>()

const defaultTab = props.tabs[0]?.value || ''
</script>

<template>
  <Tabs :default-value="defaultTab">
    <TabsList>
      <TabsTrigger
        v-for="tab in tabs"
        :key="tab.value"
        :value="tab.value"
      >
        {{ tab.label }}
        <span
          v-if="tab.count !== undefined"
          class="ml-1 text-sm font-semibold text-muted-foreground"
        >
          ({{ tab.count }})
        </span>
      </TabsTrigger>
    </TabsList>

    <TabsContent
      v-for="tab in tabs"
      :key="tab.value"
      :value="tab.value"
    >
      <component :is="tab.component" />
    </TabsContent>
  </Tabs>
</template>
