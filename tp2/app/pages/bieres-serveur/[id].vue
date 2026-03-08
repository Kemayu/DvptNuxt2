<script setup>
const route = useRoute()
const id = route.params.id

const {data: bieres} = await useFetch(`https://api.sampleapis.com/beers/ale/`)
const biere = bieres.value[id]

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