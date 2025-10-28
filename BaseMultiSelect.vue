<script setup lang="ts">
import { Select, SelectContent, SelectGroup, SelectLabel, SelectTrigger, SelectValue } from '@/components/ui/select'
import { Checkbox } from '@/components/ui/checkbox'
import { Label } from '@/components/ui/label'
import { cn } from '@/lib/utils'
import { computed } from 'vue'

const props = withDefaults(defineProps<{
  label?: string
  options?: Array<{ id: string; name: string, isSelected?: boolean }>
  placeholder?: string
  class?: string
  required?: boolean
}>(), {
  label: '',
  placeholder: '',
  class: '',
  options: () => [],
  required: false
})

const model = defineModel<string[]>({
  required: true,
  default: () => ([])
})

const handleCheckboxChange = (id: string, checked: boolean) => {
  const newValue = [...model.value]
  if (checked) {
    if (!newValue.includes(id)) newValue.push(id)
  } else {
    const index = newValue.indexOf(id)
    if (index > -1) newValue.splice(index, 1)
  }
  model.value = newValue
}

const handleSelectAllChange = (checked: boolean) => {
  if (checked) {
    // Select all option IDs
    model.value = props.options.map(option => option.id)
  } else {
    // Deselect all
    model.value = []
  }
}

const isAllSelected = computed(() => {
  return props.options.length > 0 && model.value.length === props.options.length
})

const isSomeSelected = computed(() => {
  return model.value.length > 0 && model.value.length < props.options.length
})

const displaySelected = computed(() => {
  if (model.value.length === 0) return props.placeholder
  const allNames = model.value.map(id =>
    props.options?.find(opt => opt.id === id)?.name || id
  )
  const MAX_DISPLAY = 4
  if (allNames.length > MAX_DISPLAY) {
    return allNames.slice(0, MAX_DISPLAY).join(', ') + `... (+${allNames.length - MAX_DISPLAY})`
  }
  return allNames.join(', ')
})
</script>

<template>
  <div>
    <Label v-if="label" for="assessment-title" class="mb-1">
      {{ label }}
      <span v-if="required" class="text-red-500 -ml-1">*</span>
    </Label>
    <Select>
      <SelectTrigger :class="cn('w-full truncate', props.class)">
        <SelectValue>
          {{ displaySelected }}
        </SelectValue>
      </SelectTrigger>

      <SelectContent>
        <SelectGroup>
          <slot />
          <SelectLabel class="truncate max-w-full">
            {{ placeholder || label }}
          </SelectLabel>
          
          <!-- Select All Checkbox -->
          <div 
            class="flex gap-2 items-center p-2 border-b border-gray-200" 
            @click.stop
          >
            <Checkbox 
              id="select-all" 
              :model-value="isAllSelected"
              :indeterminate="isSomeSelected"
              @update:model-value="handleSelectAllChange" 
            />
            <Label for="select-all" class="font-semibold">Select All</Label>
          </div>
          
          <!-- Individual Options -->
          <div 
            v-for="item in options" :key="item.id" 
            class="flex gap-2 items-center p-2" 
            @click.stop
          >
            <Checkbox 
              :id="item.id" 
              :model-value="model.includes(item.id)"
              @update:model-value="(checked: boolean) => handleCheckboxChange(item.id, checked)" 
            />
            <Label :for="item.id">{{ item.name }}</Label>
          </div>
        </SelectGroup>
        <slot name="add-button" />
      </SelectContent>
    </Select>
  </div>
</template>
