<script setup lang="ts">
import type { Query } from "@directus/sdk"
import type { components } from "../../interfaces/nuxtus"
type Page = components["schemas"]["ItemsPage"]
const { $directus, $readItems, $checkError } = useNuxtApp() as unknown as {
  $directus: any,
  $readItems: (collection: string, query: Query<components, Page>) => Promise<Page[]>,
  $checkError: (error: any) => void
}

const config = useRuntimeConfig()

const query: Query<components, Page> = {
  // Add your filters and query customisations here
}

const { data, error } = useAsyncData <Page[] | null> ('page', () => {
  return $directus.request($readItems('page', query))
})
$checkError(error)
</script>

<template>
  <div class="p-10">
    <div v-if="data === null || data.length > 0" class="grid gap-3">
      <NuxtLink :to="`/page/${page.slug}`" v-for="page in data" :key="page.id" class="p-4 border ">
        
          {{ page.slug }}
          {{ page.title }}
          
            <img
            v-if="page?.image"
            :src="`${config.public.DIRECTUS_URL}/assets/${page.image}`"
            :alt="page?.title ?? ''"
            class="inline-block w-20 h-20"
            >
          
        </NuxtLink>
      
    </div>
    <p v-else>No page found.</p>
  </div>
</template>