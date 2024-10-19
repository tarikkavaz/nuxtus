<script setup lang="ts">
import type { Query } from "@directus/sdk";
import type { components } from "../../interfaces/nuxtus"
type Page = components["schemas"]["ItemsPage"]
const route = useRoute()
const { $directus, $readItem, $checkError } = useNuxtApp() as unknown as {
  $directus: any,
  $readItem: (collection: string, id: string, query: Query<components, Page>) => Promise<Page>,
  $checkError: (error: any) => void
}
const config = useRuntimeConfig()

const query: Query<components, Page> = {
  // Add your filters and query customisations here
}

const { data: page, error } = useAsyncData <Page | null> ('page', () => {
  return $directus.request($readItem('page', Array.isArray(route.params.id) ? route.params.id[0] : route.params.id, query))
})
$checkError(error)
</script>

<template>
  <div class="p-10">
    <h1>{{ page?.title }}</h1>
    <div>{{ page?.id }}</div>
    <div>{{ page?.slug }}</div>
    <div v-html="page?.content"></div>
    <img
    v-if="page?.image"
    :src="`${config.public.DIRECTUS_URL}/assets/${page.image}`"
    :alt="page?.title ?? ''"
  >
  </div>
</template>
