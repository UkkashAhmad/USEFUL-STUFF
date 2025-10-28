<script setup lang="ts">
import { DropdownMenuSeparator } from '@/components/ui/dropdown-menu'

const props = withDefaults(defineProps<{
  heading?: string
  subText?: string
  direction?: 'left' | 'right' | 'top' | 'bottom'
}>(), {
  heading: 'Default Heading',
  direction: 'right'
})
const isVisible = defineModel<boolean>('visible')
</script>
<template>
    <div>
        <UDrawer v-model:open="isVisible" handle-only :direction="props.direction"  class="min-w-[400px] bg-white">
            <template #header>
                <div class="flex justify-between items-center">
                    <div class="flex flex-col">
                        <h2 class="text-xl font-semibold text-[black]">{{ props.heading }}</h2>
                        <template v-if="props.subText">
                            <p class="max-w-[450px] text-sm my-2">{{ props.subText }}</p>
                        </template>
                    </div>
                    <UButton color="neutral" variant="ghost" icon="i-lucide-x" @click="isVisible = false" />
                </div>
                <DropdownMenuSeparator />
            </template>
            <template #body>
            <!-- max-h-[76vh] -->
                <div class="max-h-[76vh] overflow-y-auto pr-2">
                    <slot name="body"/>
                </div>
            </template>
            <template #footer>
                <DropdownMenuSeparator />
                <slot name="footer"/>
            </template>
        </UDrawer>
    </div>
</template>
