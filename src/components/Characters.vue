<template>
    <div class="flex flex-col items-center">
        <h1 class="text-center text-transparent text-4xl font-bold bg-clip-text bg-gradient-to-r from-sky-700 to-sky-400 py-5 px-5 w-full">Star Wars Characters</h1>
        <div class="mt-24 w-9/12">
            <div class="mb-10 p-4 shadow-md rounded-md" v-for="(detail, i) in testCharacters" :key="i">
                <div class="name-container text-xl font-semibold text-sky-700 " @click="getDetails(detail, i)">
                    {{ detail.name }}
                </div>

                <div class="details-container font-normal mt-5" v-if="selectedItem == i && this.open">
                    <ul class="characterDetails" >
                        <li> {{ "Birth Year: " + detail.birth_year }} </li>
                        <li> {{ "Eye Color: " + detail.eye_color }} </li>
                        <li> {{ "Films: " + detail.films }} </li>
                        <li> {{ "Gender : " + detail.gender }} </li>
                        <li> {{ "Height: " + detail.height }} </li>
                        <li> {{ "Homeworld: " + detail.homeworld }} </li>
                        <li> {{ "Mass: " + detail.mass }} </li>
                        <li> {{ "Skin Color: " + detail.skin_color }} </li>
                        <li> {{ "Species: " + detail.species }} </li>
                        <li> {{ "Starships: " + detail.starships }} </li>
                        <li> {{ "Vehicles: " + detail.vehicles }} </li>
                    </ul>
                </div>
            </div>
        </div>

        <button @click="more" class="btn-more font-semibold bg-sky-700 hover:bg-sky-500 mb-10 text-white px-5 py-2 rounded-full shadow-md">More Characters</button>
    </div>
</template>

<script>
import axios from "axios"
const apiUrl = "https://swapi.dev/api/"
export default {
    data() {
        return {
            testCharacters: [],
            addition: [],
            movies: "",
            nextUrl: "",
            filteredDetails: [],
            detail: "",
            selectedItem: null,
            open: false,
        };
    },
    async created() {
        await axios
            .get(`${apiUrl}/people/`)
            .then((response) => {
            this.testCharacters = response.data.results;
            this.nextUrl = response.data.next;
            console.log(this.testCharacters);
            for (let i = 0; i < response.data.results.length; i++) {
                axios.get(`${response.data.results[i].homeworld}`)
                    .then((response) => {
                    this.testCharacters[i].homeworld = response.data.name;
                });
                let numFilms = response.data.results[i].films.length;
                for (let j = 0; j < numFilms; j++) {
                    this.getFilms(i, j);
                }
                let numStarships = response.data.results[i].starships.length;
                for (let j = 0; j < numStarships; j++) {
                    this.getStarships(i, j);
                }
                let numVehicles = response.data.results[i].vehicles.length;
                for (let j = 0; j < numVehicles; j++) {
                    this.getVehicles(i, j);
                }
                this.getSpecies(i);
            }
        });
    },
    methods: {
        async more() {
            await axios
                .get(this.nextUrl)
                .then((response) => {
                this.addition = response.data.results;
                for (let i = 0; i < response.data.results.length; i++) {
                    axios.get(`${response.data.results[i].homeworld}`)
                        .then((response) => {
                        this.addition[i].homeworld = response.data.name;
                    });
                    let numFilms = response.data.results[i].films.length;
                    for (let j = 0; j < numFilms; j++) {
                        axios
                            .get(`${this.addition[i].films[j]}`)
                            .then((response) => {
                            this.addition[i].films[j] = response.data.title;
                        });
                    }
                    let numStarships = response.data.results[i].starships.length;
                    for (let j = 0; j < numStarships; j++) {
                        axios
                            .get(`${this.addition[i].starships[j]}`)
                            .then((response) => {
                            this.addition[i].starships[j] = response.data.name;
                        });
                    }
                    axios
                        .get(`${this.addition[i].species}`)
                        .then((response) => {
                        this.addition[i].species = response.data.name;
                    });
                    let numVehicles = response.data.results[i].vehicles.length;
                    for (let j = 0; j < numVehicles; j++) {
                        axios
                            .get(`${this.addition[i].vehicles[j]}`)
                            .then((response) => {
                            console.log(response.data.results);
                            this.addition[i].vehicles[j] = response.data.name;
                        });
                    }
                }
                this.testCharacters = this.testCharacters.concat(this.addition);
                if (response.data.next != null) {
                    this.nextUrl = response.data.next;
                    console.log(this.testCharacters);
                }
                else {
                    console.log("No more characters");
                }
            });
        },
        async getStarships(id, num) {
            await axios
                .get(`${this.testCharacters[id].starships[num]}`)
                .then((response) => {
                this.testCharacters[id].starships[num] = response.data.name;
            });
        },
        async getFilms(id, num) {
            await axios
                .get(`${this.testCharacters[id].films[num]}`)
                .then((response) => {
                this.testCharacters[id].films[num] = response.data.title;
            });
        },
        async getSpecies(id) {
            await axios
                .get(`${this.testCharacters[id].species}`)
                .then((response) => {
                this.testCharacters[id].species = response.data.name;
            });
        },
        async getVehicles(id, num) {
            await axios
                .get(`${this.testCharacters[id].vehicles[num]}`)
                .then((response) => {
                this.testCharacters[id].vehicles[num] = response.data.name;
            });
        },
        getDetails(detail, i) {
            this.selectedItem = i;
            this.detail = detail.name
            this.open = !this.open
        }
    },
}

</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>