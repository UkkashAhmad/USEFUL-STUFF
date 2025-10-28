Do create items array in component for this and done

<script setup lang="ts">
import {
  Breadcrumb,
  BreadcrumbItem,
  BreadcrumbLink,
  BreadcrumbList,
  BreadcrumbPage,
  BreadcrumbSeparator,
} from '@/components/ui/breadcrumb'

interface BreadcrumbItemType {
  title: string
  href?: string 
}

defineProps<{
  items: BreadcrumbItemType[]
}>()

</script>

<template>
  <div class="bg-gray-200 p-2">
    <Breadcrumb>
      <BreadcrumbList>
        <template v-for="(item, index) in items" :key="index">
          <BreadcrumbItem>
            <template v-if="item.href && index !== items.length - 1">
              <!-- <BreadcrumbLink :href="item.href">
                {{ item.title }}
              </BreadcrumbLink> -->
              <NuxtLink :to="item.href">
                {{ item.title }}
              </NuxtLink>
            </template>
            <template v-else>
              <BreadcrumbPage>{{ item.title }}</BreadcrumbPage>
            </template>
          </BreadcrumbItem>
          <template v-if="index < items.length - 1">
            <BreadcrumbSeparator />
          </template>
        </template>
      </BreadcrumbList>
    </Breadcrumb>
  </div>
</template>
