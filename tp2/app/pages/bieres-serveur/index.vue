<script setup>
const route = useRoute()

const page = route.query.page ? Number(route.query.page) : 1
const itemsParPage = 10

const bierType = route.query.type || 'ale'

const apiUrl = bierType === 'ale' 
  ? 'https://api.sampleapis.com/beers/ale'
  : 'https://api.sampleapis.com/beers/stouts'

const { data: toutesLesData } = await useFetch(apiUrl)

const start = (page - 1) * itemsParPage
const bieresPaginees = toutesLesData.value?.slice(start, start + itemsParPage) || []
const totalPages = Math.ceil((toutesLesData.value?.length || 0) / itemsParPage)
</script>

<template>
    <div>
      <h1>Bières ({{ bierType === 'ale' ? 'Ale' : 'Stouts' }})</h1>
      
      <div>
        <NuxtLink :to="{ query: { ...route.query, type: 'ale', page: 1 } }">Ale</NuxtLink>
        |
        <NuxtLink :to="{ query: { ...route.query, type: 'stouts', page: 1 } }">Stouts</NuxtLink>
      </div>
    </div>

    <div v-for="biere in bieresPaginees" :key="biere.id" style="border: 1px solid #ccc; padding: 10px; margin: 10px 0;">
      <h2>{{ biere?.name }}</h2>
      <ul>
        <li>ID: {{ biere?.id }}</li>
        <li>Prix: {{ biere?.price }}</li>
        <li>Moyenne: {{ biere?.rating.average }}</li>
        <li>Notes: {{ biere?.rating.reviews }}</li>
      </ul>
      <img :src="biere?.image" alt="photoBiere" style="width: 100px;">
      <NuxtLink :to="`/bieres-serveur/${biere.id}`">Voir détail →</NuxtLink>
    </div>

    <div v-if="totalPages > 1" style="margin-top: 20px;">
      <NuxtLink 
        v-if="page > 1"
        :to="{ query: { ...route.query, page: page - 1 } }"
      >
        ← Précédent
      </NuxtLink>
      
      <span style="margin: 0 10px;"> Page {{ page }} / {{ totalPages }} </span>
      
      <NuxtLink 
        v-if="page < totalPages"
        :to="{ query: { ...route.query, page: page + 1 } }"
      >
        Suivant →
      </NuxtLink>
    </div>

</template>
