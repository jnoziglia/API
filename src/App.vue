<script setup>
import PlanetsList from './components/PlanetsList.vue'
</script>

<template>
    <header>
      <div class="text-h1 text-yellow">API</div>
      <div class="text-subtitle-1">Accurate Planetary Information</div>
    </header>
    <main>
      <PlanetsList />
    </main>
  
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

main {
  min-width: 50px;
}

header {
  text-align: center;
}

</style>
