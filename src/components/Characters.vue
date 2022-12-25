<template>
    <h1>Star Wars Characters</h1>
    <!-- <ul class="characters" v-for="item in testCharacters" :key="item.id"> -->
    <div v-for="(detail, i) in testCharacters" :key="i" @click="getDetails($event)">{{ detail.name }}</div>
    <ul class="characterDetails" v-for="(detail, i) in filteredDetails" :key="i">
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
    <!-- </ul> -->
    <button @click="more" class="btn-more">More Characters</button>
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
            nextUrl : "",
            filteredDetails: []
        }
    },
    async created() {
        await axios 
            .get(`${apiUrl}/people/`)
            .then((response) => {
                this.testCharacters = response.data.results
                this.nextUrl = response.data.next
                console.log(this.testCharacters)

                for (let i = 0; i < response.data.results.length; i++) {
                    axios.get(`${response.data.results[i].homeworld}`)
                    .then((response) => {
                        this.testCharacters[i].homeworld = response.data.name
                    })
                    let numFilms = response.data.results[i].films.length
                    for (let j = 0; j < numFilms; j++) {
                        this.getFilms(i, j)
                    }
                    let numStarships = response.data.results[i].starships.length
                    for (let j = 0; j < numStarships; j++) {
                        this.getStarships(i,j)
                    }
                    let numVehicles = response.data.results[i].vehicles.length
                    for (let j = 0; j < numVehicles; j++) {
                        this.getVehicles(i,j)
                    }
                    this.getSpecies(i)
                }
            })
    },

    methods: {
        async more() {
            await axios
            .get(this.nextUrl)
            .then((response) => {
                this.addition = response.data.results

                for (let i = 0; i < response.data.results.length; i++) {
                    axios.get(`${response.data.results[i].homeworld}`)
                    .then((response) => {
                        this.addition[i].homeworld = response.data.name
                    })

                    let numFilms = response.data.results[i].films.length
                    for (let j = 0; j < numFilms; j++) {
                        axios
                        .get(`${this.addition[i].films[j]}`)
                        .then((response) => {
                            this.addition[i].films[j] = response.data.title
                        })
                    }

                    let numStarships = response.data.results[i].starships.length
                    for (let j = 0; j < numStarships; j++) {
                        axios
                        .get(`${this.addition[i].starships[j]}`)
                        .then((response) => {
                            this.addition[i].starships[j] = response.data.name
                        })
                    }

                    axios
                    .get(`${this.addition[i].species}`)
                    .then((response) => {
                        this.addition[i].species = response.data.name
                    })

                    let numVehicles = response.data.results[i].vehicles.length
                    for (let j = 0; j < numVehicles; j++) {
                        axios
                        .get(`${this.addition[i].vehicles[j]}`)
                        .then((response) => {
                            console.log(response.data.results)
                            this.addition[i].vehicles[j] = response.data.name
            })
                    }
                    


                }
                this.testCharacters = this.testCharacters.concat(this.addition)
                if (response.data.next != null) {
                    this.nextUrl = response.data.next
                    console.log(this.testCharacters)
                } else {
                    console.log("No more characters")
                    //zjebat gumb nekak
                }
            })
        },

        async getStarships(id, num) {
            await axios
            .get(`${this.testCharacters[id].starships[num]}`)
            .then((response) => {
                this.testCharacters[id].starships[num] = response.data.name
            })
        },

        async getFilms(id, num) {
            await axios
            .get(`${this.testCharacters[id].films[num]}`)
            .then((response) => {
                this.testCharacters[id].films[num] = response.data.title
            })
        },

        async getSpecies(id) {
            await axios
            .get(`${this.testCharacters[id].species}`)
            .then((response) => {
                this.testCharacters[id].species = response.data.name
            })
        },

        async getVehicles(id, num) {
            await axios
            .get(`${this.testCharacters[id].vehicles[num]}`)
            .then((response) => {
                this.testCharacters[id].vehicles[num] = response.data.name
            })
        },

        getDetails(e) {
            this.filteredDetails = this.testCharacters.filter((obj) => obj.name === e.target.textContent);
        }
    }      
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>