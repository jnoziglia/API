<script setup>
    import { ref } from 'vue';
    const planets = ref(null);
    fetch('https://swapi.dev/api/planets')
        .then(response => response.json())
        .then(data => planets.value = data);
</script>

<template>
    <div v-if="planets">
        <h5>Planets</h5>
        <ul class="mb-0">
            <li v-for="planet in planets.results" :key="planet.id">{{planet.name}}</li>
        </ul>
    </div>
</template>