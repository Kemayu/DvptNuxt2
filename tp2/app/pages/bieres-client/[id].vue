<script setup>
import { ref, onMounted, computed } from 'vue'
const route = useRoute()
const id = route.params.id

const bieres = ref(null)
const biere = computed(() => bieres.value?.[id])

const getBiere = async () => {
  bieres.value = await $fetch(`https://api.sampleapis.com/beers/ale/`)
}

onMounted(getBiere)
</script>
<template>
    <h2>{{biere?.name}}</h2>
    <ul>
        <li>ID: {{biere?.id}}</li>
        <li>Prix: {{biere?.price}}</li>
        <li>Moyenne: {{biere?.rating.average}}</li>
        <li>Notes: {{biere?.rating.reviews}}</li>
      
    </ul>
    <img :src="biere?.image" alt="photoBiere">
</template>