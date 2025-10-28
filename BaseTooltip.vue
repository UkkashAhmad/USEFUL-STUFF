<script setup lang="ts">
import { Tooltip, TooltipContent, TooltipTrigger } from '@/components/ui/tooltip'

const props = defineProps({
  title: {
    type: String,
    required: true,
  },
  toolTipContent: {
    type: String,
    required: true,
  },
});
</script>

<template>
    <div>
        <Tooltip>
            <TooltipTrigger as-child>
                <p>{{ props.title }}</p>
            </TooltipTrigger>
            <TooltipContent>
                <p>{{ props.toolTipContent }}</p>
            </TooltipContent>
        </Tooltip>
    </div>
</template>
