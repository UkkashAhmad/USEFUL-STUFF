<script setup lang="ts">
import { Check } from 'lucide-vue-next'
import {
  Stepper,
  StepperItem,
  StepperSeparator,
  StepperTitle,
  StepperTrigger,
} from '@/components/ui/stepper'

const stepIndex = defineModel<number>('stepIndex')

const props = defineProps<{
  steps: { step: number; title: string }[]
  triggerNext: boolean
  triggerPrev: boolean
}>()

const emit = defineEmits([
  'update:triggerNext',
  'update:triggerPrev'])

let internalNext: () => void
let internalPrev: () => void

watch(
  () => props.triggerNext,
  (val) => {
    if (val && internalNext) {
      internalNext()
      emit('update:triggerNext', false)
    }
  }
)

watch(
  () => props.triggerPrev,
  (val) => {
    if (val && internalPrev) {
      internalPrev()
      emit('update:triggerPrev', false)
    }
  }
)
</script>

<template>
  <Stepper
    v-slot="{ nextStep, prevStep }"
    v-model="stepIndex"
    class="block w-full mt-5"
  >
    <component :is="() => {
      internalNext = nextStep
      internalPrev = prevStep
    }" />

    <div class="flex justify-between items-center w-full">
      <StepperItem
        v-for="(step, index) in steps"
        :key="step.step"
        v-slot="{ state }"
        class="flex items-center flex-1 last:flex-none"
        :step="step.step"
      >
        <StepperTrigger as-child>
          <Button
            size="icon"
            class="z-10 rounded-full shrink-0"
            :variant="state === 'completed' || state === 'active' ? 'default' : 'outline'"
            :class="[
              state === 'active' && 'bg-white border-[#027373] border-2 hover:bg-muted text-gray-500',
              state === 'completed' && 'bg-[#027373] hover:bg-[#027373] text-white',
              state === 'inactive' && 'bg-white text-gray-500'
            ]"
          >
            <Check v-if="state === 'completed'" class="size-5" />
            <p 
              v-if="state === 'inactive' || state === 'active'"
              class="text-black"
            >
              {{step.step.toString().padStart(2,"0")}}
            </p>
          </Button>
        </StepperTrigger>

        <div class="">
          <StepperTitle
            :class="[(state === 'completed' || state === 'active') ? 'text-black' : 'text-[#999999]']"
            class="text-sm font-semibold transition whitespace-nowrap"
          >
            {{ step.title }}
          </StepperTitle>
        </div>

        <StepperSeparator
          v-if="index !== steps.length - 1"
          class="flex-1 h-1.5 mx-4 rounded-full bg-[#E8EAF2] data-[state=completed]:bg-[#027373]"
        />
      </StepperItem>
    </div>
  </Stepper>
</template>
