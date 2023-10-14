<script setup>
    import Pagination from './Pagination.vue'
    import { ref } from 'vue';
    let allPlanets = [];
    const planets = ref(null);
    const loading = ref(false);
    const planetCount = ref(0);
    const resultsPerPage = ref(10);
    
    loading.value = true;

    allPlanets = getPlanets('https://swapi.dev/api/planets', []);
    console.log(allPlanets);
    loading.value = false;
    planetCount.value = allPlanets.length;
    planets.value = allPlanets;

    function getPlanets(url, planetList) {
        fetch(url)
            .then(response => response.json())
            .then(data => {
                if (data.next) {
                    return getPlanets(data.next, planetList.concat(data.results));
                }
                else {
                    return planetList.concat(data.results);
                }
            });
    }

    const sortPlanets = () => {
        planets.value.results.sort((a, b) => {
            return a.name.localeCompare(b.name);
        });
    }

    const updateList = (page) => {
        console.log(page);
        loading.value = true;
        fetch(`https://swapi.dev/api/planets/?page=${page}`)
            .then(response => response.json())
            .then(data => {
                planets.value = data;
                loading.value = false;
            });
    }
    </script>

    <template>
        <div>
            <button role="button" @click="sortPlanets">Sort</button>
        </div>
        <div v-if="loading">
            Loading...
        </div>
        <div v-else-if="planets">
            <h5>Planets</h5>
            <ul class="mb-0">
                <li v-for="planet in planets" :key="planet.name">{{planet.name}}</li>
            </ul>
        </div>
        <div>
            <Pagination v-if="allPlanets.count" :total="Math.ceil(allPlanets.count/resultsPerPage)" :resultsPerPage="resultsPerPage" @updateCurrent="updateList" />
        </div>
        <div>Page</div>
    </template>

