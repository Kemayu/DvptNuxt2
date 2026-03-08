<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const route = useRoute()
const bieres = ref(null)
const favoris = ref([])
const afficherFavoris = ref(false)

const toggleFavori = (biere) => {
  const index = favoris.value.findIndex(f => f.id === biere.id)
  if (index > -1) {
    favoris.value.splice(index, 1)
  } else {
    favoris.value.push(biere)
  }
  localStorage.setItem('favoris', JSON.stringify(favoris.value))
}

const isFavori = (biereId) => favoris.value.some(f => f.id === biereId)

const bieresPourAffichage = computed(() => {
  return afficherFavoris.value ? favoris.value : bieresPaginees.value
})

const bierType = computed({
  get: () => route.query.type || 'ale',
  set: (val) => navigateTo({ query: { ...route.query, type: val } })
})

const prixMax = computed({
  get: () => Number(route.query.prixMax) || 0,
  set: (val) => navigateTo({ query: { ...route.query, prixMax: val } })
})

const page = computed({
  get: () => Number(route.query.page) || 1,
  set: (val) => navigateTo({ query: { ...route.query, page: val } })
})

const itemsParPage = 10

const bierefiltre = computed(() => bieres.value?.filter(b => Number(b.price.replace('$', '')) <= Number(prixMax.value)))

const bieresPaginees = computed(() => {
  const start = (page.value - 1) * itemsParPage
  return bierefiltre.value?.slice(start, start + itemsParPage) || []
})

const totalPages = computed(() => 
  Math.ceil((bierefiltre.value?.length || 0) / itemsParPage)
)

const getBiere = async () => {
    const apiUrl = bierType.value === 'ale' 
      ? 'https://api.sampleapis.com/beers/ale'
      : 'https://api.sampleapis.com/beers/stouts'
    bieres.value = await $fetch(apiUrl)
}

onMounted(() => {
  const favorisSauves = localStorage.getItem('favoris')
  if (favorisSauves) {
    favoris.value = JSON.parse(favorisSauves)
  }
  getBiere()
})

watch(() => bierType.value, getBiere)
</script>
<template>
    <div>
      <select v-model="bierType">
        <option value="ale">Ale</option>
        <option value="stouts">Stouts</option>
      </select>
      <input type="number" v-model="prixMax" placeholder="Prix maximum">
    </div>

    <div>
      <button @click="afficherFavoris = false">Toutes les bières</button>
      <button @click="afficherFavoris = true">Mes favoris ({{ favoris.length }})</button>
    </div>

    <div v-for="biere in bieresPourAffichage" :key="biere.id" style="border: 1px solid #ccc; padding: 10px; margin: 10px 0;">
      <div style="display: flex; justify-content: space-between;">
        <h2>{{ biere?.name }}</h2>
        <button 
          @click="toggleFavori(biere)"
          :style="{ color: isFavori(biere.id) ? 'red' : 'blue', width: '5em' }"
        >
          {{ isFavori(biere.id) ? 'Remove' : 'add' }}
        </button>
      </div>
        <ul>
            <li>ID: {{ biere?.id }}</li>
            <li>Prix: {{ biere?.price }}</li>
            <li>Moyenne: {{ biere?.rating.average }}</li>
            <li>Notes: {{ biere?.rating.reviews }}</li>
        </ul>
        <img :src="biere?.image" alt="photoBiere" style="width: 100px;">
    </div>

    <div v-if="!afficherFavoris && totalPages > 1" style="margin-top: 20px;">
      <button @click="page = Math.max(1, page - 1)" :disabled="page === 1">← Précédent</button>
      <span> Page {{ page }} / {{ totalPages }} </span>
      <button @click="page = Math.min(totalPages, page + 1)" :disabled="page === totalPages">Suivant →</button>
    </div>

</template>