<script setup lang="ts">
import type { Query } from "@directus/sdk"
import type { components } from "../../interfaces/nuxtus"
type Page = components["schemas"]["ItemsPage"]
const { $directus, $readItems, $checkError } = useNuxtApp()

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
    <ul v-if="data === null || data.length > 0">
      <li v-for="page in data" :key="page.id">
        <NuxtLink :to="`/page/${page.id}`">
          {{ page }}
        </NuxtLink>
      </li>
    </ul>
    <p v-else>No page found.</p>
  </div>
</template>