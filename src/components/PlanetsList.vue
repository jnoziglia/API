<script setup>
    import { ref } from 'vue';
    import Pagination from './Pagination.vue'
    import { VSkeletonLoader } from 'vuetify/labs/VSkeletonLoader'
    let allPlanets = [];
    const currentPage = ref(1);
    const planets = ref(null);
    const loading = ref(true);
    const planetCount = ref(0);
    const resultsPerPage = ref(10);
    const sortOrder = ref(null);
    const loadingFilms = ref(true);
    const loadingResidents = ref(true);

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
                        updateList(currentPage.value);
                        loading.value = false;
                    });
            }
        });
    
    const sortPlanets = () => {
        if(sortOrder.value === 'asc'){
            sortOrder.value = 'desc';
            allPlanets.results.sort((a, b) => {
                return b.name.localeCompare(a.name);
            });
        } else {
            sortOrder.value = 'asc';
            allPlanets.results.sort((a, b) => {
                return a.name.localeCompare(b.name);
            });
        }
        updateList(currentPage.value);
    }

    const updateList = (page) => {
        currentPage.value = page;
        let start = (page - 1) * resultsPerPage.value;
        let end = start + resultsPerPage.value;
        planets.value = allPlanets.results.slice(start, end);
    }

    const loadFilmsAndResidents = (planet) => {
        let films = [];
        let residents = [];
        if(planet.processedFilms){
            loadingFilms.value = false;
        }
        else {
            loadingFilms.value = true;
            Promise.all(planet.films.map(url => fetch(url).then(response => response.json())))
                .then(data => {
                    data.forEach(film => {
                        films.push(film.title);
                    });
                    planet.films = films;
                    planet.processedFilms = true;
                    loadingFilms.value = false;
                });
        }
        
        if(planet.processedResidents){
            loadingResidents.value = false;
        }
        else {
            loadingResidents.value = true;
            Promise.all(planet.residents.map(url => fetch(url).then(response => response.json())))
                .then(data => {
                    data.forEach(resident => {
                        residents.push(resident.name);
                    });
                    planet.residents = residents;
                    planet.processedResidents = true;
                    loadingResidents.value = false;
                });
        }
        
    }

</script>

<template>
    <v-fade-transition>
    <div key="1" v-if="loading" transition="fade-transition">
        <v-progress-linear indeterminate color="yellow"></v-progress-linear>
    </div>
    <div key="2" v-else-if="planets" class="mt-5" transition="fade-transition">
        <div class="d-flex justify-end mb-2">
            <span class="pr-2">Sort: </span>
            <v-icon v-if="sortOrder == 'desc'" @click="sortPlanets()" icon="mdi-sort-alphabetical-descending-variant"></v-icon>
            <v-icon v-else-if="sortOrder == 'asc'" @click="sortPlanets()" icon="mdi-sort-alphabetical-ascending-variant"></v-icon>
            <v-icon v-else @click="sortPlanets()" icon="mdi-sort-alphabetical-variant"></v-icon>
        </div>
        <div v-for="(planet, i) in planets" :key="i" class="mb-3">
            <v-card variant="outlined">
                <v-card-item>
                    <v-card-title class="text-h5">{{ planet.name }}</v-card-title>
                    <v-card-subtitle class="d-flex flex-wrap">
                        <div class="ma-2 pa-2"><v-icon class="mr-1" icon="mdi-image-filter-hdr"></v-icon>{{ planet.terrain.charAt(0).toUpperCase() + planet.terrain.slice(1) }}</div>
                        <div class="ma-2 pa-2"><v-icon class="mr-1" icon="mdi-account-multiple"></v-icon>{{ planet.population }}</div>
                        <div class="ma-2 pa-2"><v-icon class="mr-1" icon="mdi-thermometer"></v-icon>{{ planet.climate.charAt(0).toUpperCase() + planet.climate.slice(1) }}</div>
                    </v-card-subtitle>
                </v-card-item>
                <v-card-actions>
                    <v-dialog width="500" :content-props="planet">
                        <template v-slot:activator="{ props }">
                            <v-btn v-bind="props" text="More information" @click="loadFilmsAndResidents(planet)"> </v-btn>
                        </template>

                        <template v-slot:default="{ isActive }">
                            <v-card :title="planet.name">
                            <v-card-text>
                                <v-table density="compact">
                                <tbody>
                                    <tr>
                                        <td>Rotation period</td>
                                        <td>{{ planet.rotation_period }}</td>
                                    </tr>
                                    <tr>
                                        <td>Orbital period</td>
                                        <td>{{ planet.orbital_period }}</td>
                                    </tr>
                                    <tr>
                                        <td>Diameter</td>
                                        <td>{{ planet.diameter }}</td>
                                    </tr>
                                    <tr>
                                        <td>Climate</td>
                                        <td>{{ planet.climate }}</td>
                                    </tr>
                                    <tr>
                                        <td>Gravity</td>
                                        <td>{{ planet.gravity }}</td>
                                    </tr>
                                    <tr>
                                        <td>Terrain</td>
                                        <td>{{ planet.terrain }}</td>
                                    </tr>
                                    <tr>
                                        <td>Surface water</td>
                                        <td>{{ planet.surface_water }}</td>
                                    </tr>
                                    <tr>
                                        <td>Population</td>
                                        <td>{{ planet.population }}</td>
                                    </tr>
                                    <tr>
                                        <td>Films</td>
                                        <td>
                                            <v-list lines="one">
                                                <v-list-item
                                                        v-for="(film, i) in planet.films"
                                                        :key="i"
                                                    >
                                                <v-skeleton-loader type="text" :loading="loadingFilms"> {{ film }} </v-skeleton-loader>
                                                </v-list-item>
                                            </v-list>    
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>Residents</td>
                                        <td>
                                            <v-list lines="one">
                                                <v-list-item
                                                        v-for="(resident, i) in planet.residents"
                                                        :key="i"
                                                    >
                                                <v-skeleton-loader type="text" :loading="loadingResidents"> {{ resident }} </v-skeleton-loader>
                                                </v-list-item>
                                            </v-list>    
                                        </td>
                                    </tr>
                                </tbody>
                            </v-table>
                                
                            </v-card-text>

                            <v-card-actions>
                                <v-spacer></v-spacer>

                                <v-btn
                                text="Close"
                                @click="isActive.value = false"
                                ></v-btn>
                            </v-card-actions>
                            </v-card>
                        </template>
                        </v-dialog>
                </v-card-actions>
            </v-card>
        </div>
        <Pagination :total="Math.ceil(planetCount/resultsPerPage)" :resultsPerPage="resultsPerPage" @updateCurrent="updateList" />
    </div>
    </v-fade-transition>
</template>

