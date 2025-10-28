<script setup lang="ts">
import { Dialog, DialogContent, DialogFooter, DialogHeader, DialogTitle } from '@/components/ui/dialog'

const props = withDefaults(defineProps<{
  title?: string
  class?:string
}>(), {
  title: 'Default Title',
  class: 'max-w-[800px]'
})

const isVisible = defineModel<boolean>('visible')
</script>

<template>
  <Dialog v-model:open="isVisible">
    <DialogContent class="text-center" :class="props.class">
      <DialogHeader>
        <DialogTitle class="text-center">{{ props.title }}</DialogTitle>
      </DialogHeader>
      <slot name="content"/>
      <DialogFooter>
        <slot name="footer"/>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>
