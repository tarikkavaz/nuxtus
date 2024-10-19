<script setup lang="ts">
import type { Query } from "@directus/sdk";
import type { components } from "../../interfaces/nuxtus";
import { computed } from 'vue';

type Page = components["schemas"]["ItemsPage"];
const route = useRoute();
const slug = Array.isArray(route.params.slug) ? route.params.slug[0] : route.params.slug;

const { $directus, $readItems, $checkError } = useNuxtApp() as unknown as {
  $directus: any,
  $readItems: (collection: string, query: Query<components, Page>) => Promise<Page[]>,
  $checkError: (error: any) => void
};

const config = useRuntimeConfig();

const query: Query<components, Page> = {
  filter: {
    slug: {
      _eq: slug,
    },
  },
  // Add your filters and query customisations here
};

const { data: pagesData, error } = useAsyncData<Page[] | null>('page', () => {
  return $directus.request($readItems('page', query));
});

$checkError(error);

const page = computed(() =>
  pagesData.value && pagesData.value.length > 0 ? pagesData.value[0] : null
);
</script>

<template>
  <div v-if="page" class="p-10">
    <h1>Title: {{ page.title }}</h1>
    <div>ID: {{ page.id }}</div>
    <div>Slug: {{ page.slug }}</div>
    <div>
      Content:
      <div v-html="page.content" class="p-4 border"></div>
    </div>
    <div v-if="page.image">
      <img
        :src="`${config.public.DIRECTUS_URL}/assets/${page.image}`"
        :alt="page.title ?? ''"
      >
    </div>
  </div>
  <div v-else>
    <p>Page not found.</p>
  </div>
</template>
