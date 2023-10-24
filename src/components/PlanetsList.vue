<script setup>
    import { ref } from 'vue';
    import { VDataTable } from 'vuetify/labs/VDataTable'
    let allPlanets = [];
    let headers = [
          {
            title: 'Planet',
            align: 'start',
            key: 'name',
          },
          { title: 'Rotation period', align: 'end', key: 'rotation_period' },
          { title: 'Orbital period', align: 'end', key: 'orbital_period' },
          { title: 'Diameter', align: 'end', key: 'diameter' },
          { title: 'Climate', align: 'end', key: 'climate' },
          { title: 'Gravity', align: 'end', key: 'gravity' },
          { title: 'Terrain', align: 'end', key: 'terrain' },
          { title: 'Surface water', align: 'end', key: 'surface_water' },
          { title: 'Population', align: 'end', key: 'population' },
        ]
    const currentPage = ref(1);
    const planets = ref(null);
    const loading = ref(true);
    const planetCount = ref(0);
    const resultsPerPage = ref(10);

    //allPlanets = getPlanets('https://swapi.dev/api/planets', []);
    fetch('https://swapi.dev/api/planets')
        .then(response => response.json())
        .then(data => {
            allPlanets = data;
            planetCount.value = data.count;
            let pages = Math.ceil(data.count/resultsPerPage.value);
            if(pages > 1){
                let urls = [];
                for(let i = 2; i <= pages; i++) {
                    urls.push(`https://swapi.dev/api/planets/?page=${i}`);
                }
                Promise.all(urls.map(url => fetch(url).then(response => response.json())))
                    .then(data => {
                        data.forEach(page => {
                            allPlanets.results = allPlanets.results.concat(page.results);
                            planetCount.value = allPlanets.results.length;
                        });
                        planets.value = allPlanets.results;
                        loading.value = false;
                    });
            }
        });
    </script>

    <template>
        <div v-if="loading">
            <v-progress-linear indeterminate color="yellow"></v-progress-linear>
        </div>
        <v-data-table
            v-else-if="planets"
            v-model:items-per-page="resultsPerPage"
            :headers="headers"
            :items="planets"
            item-key="name"
            class="elevation-1"
            density="compact"
        ></v-data-table>
    </template>

