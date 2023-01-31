<template>
    <header class="header">
        <label for="pokemonInput">
            Escribe nombre o número de Pokemon:
            <input type="text" id="pokemonInput" v-model="pokemonID" />
        </label>
        <button class="searchButton" @click="searchPokemon">Buscar</button>
    </header>
    <main class="main">
        <section class="pokemonCard">
            <input
                type="button"
                v-for="(type, key) in arrayOfTypes"
                :class="type"
                :key="key"
                @click="setFilteredPokemons(type)"
                :value="type"
            />
        </section>
    </main>
    <div class="main" style="padding-top: 10px">
        <section class="pokemonCard">
            <input
                type="button"
                v-for="(pokemon, key) in arrayOfPokemonLetters"
                :class="pokemon"
                :key="key"
                @click="getPokemonsByLetter(pokemon)"
                :value="pokemon"
            />
        </section>
    </div>
    <br />
    <main
        class="main"
        v-if="Object.entries(pokemonData).length > 0 && showPokemonData == true"
    >
        <section class="pokemonCard">
            <div class="nameImage">
                <h1 class="pokemonName">{{ pokemonData.name }}</h1>
                <img
                    :src="pokemonData.sprites.front_default"
                    :alt="pokemonData.name"
                />
            </div>
            <ul class="type">
                <h2>Type:</h2>
                <li
                    v-for="(type, key) in pokemonData.types"
                    :key="key"
                    :class="type.type.name"
                >
                    <span>{{ type.type.name }}</span>
                </li>
            </ul>
            <ul class="stats">
                <h2>Stats:</h2>
                <li v-for="(stat, key) in pokemonData.stats" :key="key">
                    <span>{{ stat.stat.name }} -> {{ stat.base_stat }}</span>
                </li>
            </ul>
        </section>
    </main>
    <main v-else-if="searchLoading"><h1>Cargando...</h1></main>
    <main
        class="main"
        v-if="
            Object.entries(allPokemonData).length > 0 && showAllPokemons == true
        "
    >
        <section
            class="pokemonCard"
            v-for="(pokemonData, key) in allPokemonData"
            :key="key"
        >
            <div class="nameImage">
                <h1 class="pokemonName">{{ pokemonData.name }}</h1>
                <img
                    :src="pokemonData.sprites.front_default"
                    :alt="pokemonData.name"
                />
            </div>
            <ul class="type">
                <h2>Type:</h2>
                <li
                    v-for="(type, key) in pokemonData.types"
                    :key="key"
                    :class="type.type.name"
                >
                    <span>{{ type.type.name }}</span>
                </li>
            </ul>
            <ul class="stats">
                <h2>Stats:</h2>
                <li v-for="(stat, key) in pokemonData.stats" :key="key">
                    <span>{{ stat.stat.name }} -> {{ stat.base_stat }}</span>
                </li>
            </ul>
        </section>
    </main>
    <main class="main" v-else-if="loadingPokemons">
        <h1>Cargando todos los Pokemon...</h1>
    </main>

    <main
        class="main"
        v-if="
            Object.entries(filteredPokemonData).length > 0 &&
            showPokemonByType == true &&
            clickOnType == true
        "
    >
        <section
            class="pokemonCard"
            v-for="(pokemonData, key) in filteredPokemonData"
            :key="key"
        >
            <div class="nameImage">
                <h1 class="pokemonName">{{ pokemonData.name }}</h1>
                <img
                    :src="pokemonData.sprites.front_default"
                    :alt="pokemonData.name"
                />
            </div>
            <ul class="type">
                <h2>Type:</h2>
                <li
                    v-for="(type, key) in pokemonData.types"
                    :key="key"
                    :class="type.type.name"
                >
                    <span>{{ type.type.name }}</span>
                </li>
            </ul>
            <ul class="stats">
                <h2>Stats:</h2>
                <li v-for="(stat, key) in pokemonData.stats" :key="key">
                    <span>{{ stat.stat.name }} -> {{ stat.base_stat }}</span>
                </li>
            </ul>
        </section>
    </main>
    <main class="main" v-else-if="loadingPokemonsByType">
        <h1>Cargando Pokemons...</h1>
    </main>

    <main
        class="main"
        v-if="
            Object.entries(pokemonsWithLetterC).length > 0 &&
            clickOnLetter == true &&
            showPokemonWithC == true
        "
    >
        <h1>
            Los pokemones que contienen la letra 'C' son:
            {{ pokemonsWithLetterC.length }}
        </h1>
        <ul>
            <li v-for="(element, key) in pokemonsWithLetterC" :key="key">
                {{ key + 1 }}.-{{ element }}
            </li>
        </ul>
    </main>
    <main
        class="main"
        v-if="
            Object.entries(pokemonsWithLetterM).length > 0 &&
            clickOnLetter == true &&
            showPokemonWithM == true
        "
    >
        <h1>
            Los pokemones que contienen la letra 'M' son:
            {{ pokemonsWithLetterM.length }}
        </h1>
        <ul>
            <li v-for="(element, key) in pokemonsWithLetterM" :key="key">
                {{ key + 1 }}.-{{ element }}
            </li>
        </ul>
    </main>
</template>

<script>
import { reactive } from 'vue'
const url = 'https://pokeapi.co/api/v2/pokemon'

export default {
    name: 'App',
    data() {
        return {
            pokemonData: {},
            allPokemon: undefined,
            allPokemonData: [],
            filteredPokemonData: [],
            pokemonsWithLetterC: [],
            pokemonsWithLetterM: [],
            pokemonID: '',
            showPokemonData: false,
            showAllPokemons: false,
            showPokemonByType: false,
            showPokemonWithC: false,
            showPokemonWithM: false,
            allPokemonUrl:
                'https://pokeapi.co/api/v2/pokemon/?limit=100&offset=0',
            arrayOfTypes: [
                'Normal',
                'Fire',
                'Water',
                'Grass',
                'Electric',
                'Ice',
                'Fighting',
                'Poison',
                'Ground',
                'Flying',
                'Psychic',
                'Bug',
                'Rock',
                'Ghost',
                'Dark',
                'Dragon',
                'Steel',
                'Fairy',
                'All',
            ],
            arrayOfPokemonLetters: [
                'Pokemons that contains "C"',
                'Pokemons that contains "M"',
            ],
            searchLoading: false,
            loadingPokemons: true,
            loadingPokemonsByType: false,
            clickOnType: false,
            clickOnLetter: false,
        }
    },
    async created() {
        this.getAllPokemons()
        this.pokemonsWithM()
        this.pokemonsWithC()
        this.filterByType()
    },
    methods: {
        async searchPokemon() {
            try {
                this.searchLoading = true
                this.loadingPokemons = false
                const pokemonToFind = await fetch(`${url}/${this.pokemonID}`)
                const pokemon = await pokemonToFind.json()
                if (pokemon) {
                    this.pokemonData = pokemon
                    this.showPokemonData = true
                    this.showAllPokemons = false
                    this.showPokemonByType = false
                    this.showPokemonWithC = false
                    this.showPokemonWithM = false
                    this.searchLoading = false
                    console.log(pokemon)
                }
            } catch (error) {
                console.log(error)
                this.showPokemonData = false
                this.searchLoading = false
                alert('Pokemon was not found')
            }
        },
        async getAllPokemons() {
            try {
                this.loadingPokemons = true
                const newArray = reactive([])
                while (this.allPokemonUrl) {
                    const response = await fetch(this.allPokemonUrl)
                    const data = await response.json()
                    const results = data.results
                    this.allPokemonUrl = data.next
                    newArray.push(...results)
                }
                this.allPokemon = newArray
                this.showAllPokemons = true
                this.showPokemonData = false
                this.showPokemonByType = false
                this.showPokemonWithC = false
                this.showPokemonWithM = false
                return newArray
            } catch (error) {
                console.log(error)
            }
        },
        async pokemonsWithC() {
            const pokemons = await this.getAllPokemons()
            const pokemonsFilterWithC = pokemons
                .filter((element) => element.name.includes('c'))
                .map((pokemon) => pokemon.name)
            this.pokemonsWithLetterC = pokemonsFilterWithC
            console.log(pokemonsFilterWithC)
        },
        async pokemonsWithM() {
            const pokemons = await this.getAllPokemons()
            const pokemonsFilterWithM = pokemons
                .filter((element) => element.name.includes('m'))
                .map((pokemon) => pokemon.name)
            this.pokemonsWithLetterM = pokemonsFilterWithM
            console.log(pokemonsFilterWithM)
        },
        async filterByType(pokemonType) {
            try {
                const pokemons =
                    this.allPokemon || (await this.getAllPokemons())
                console.log(pokemons)
                const pokemonDataArray = reactive([])
                if (this.allPokemonData.length < 1) {
                    console.log('entroaca')
                    for (let i = 0; i < pokemons.length; i++) {
                        console.log(i)
                        let result = await fetch(`${url}/${pokemons[i].name}`)
                        let data = await result.json()
                        pokemonDataArray.push(data)
                    }
                    this.allPokemonData = pokemonDataArray
                    console.log(this.allPokemonData)
                }

                if (pokemonType && pokemonType !== 'All') {
                    const filteredPokemons = this.allPokemonData.filter(
                        (pokemon) =>
                            pokemon.types.some(
                                (type) =>
                                    type['type']['name'] ==
                                    pokemonType.toLowerCase()
                            )
                    )
                    console.log(filteredPokemons)
                    this.filteredPokemonData = filteredPokemons
                    return filteredPokemons
                } else {
                    this.filteredPokemonData = this.allPokemonData
                    return this.allPokemonData
                }
            } catch (error) {
                console.log(error)
            }
        },
        async setFilteredPokemons(type) {
            this.loadingPokemonsByType = true
            this.loadingPokemons = false
            this.showPokemonData = false
            this.showAllPokemons = false
            this.showPokemonWithC = false
            this.showPokemonWithM = false
            const filteredPokemonsData = await this.filterByType(type)
            if (filteredPokemonsData) {
                this.clickOnType = true
                this.showPokemonByType = true
                this.loadingPokemonsByType = false
                return filteredPokemonsData
            }
        },
        async getPokemonsByLetter(selectedButton) {
            this.clickOnLetter = true
            this.searchLoading = false
            this.loadingPokemons = false
            this.loadingPokemonsByType = false
            if (selectedButton === 'Pokemons that contains "C"') {
                const pokemons = this.pokemonsWithLetterC
                this.showPokemonWithC = true
                this.showPokemonWithM = false
                this.showPokemonData = false
                this.showAllPokemons = false
                this.showPokemonByType = false
                return pokemons
            } else {
                const pokemons = this.pokemonsWithLetterM
                this.showPokemonWithM = true
                this.showPokemonWithC = false
                this.showPokemonData = false
                this.showAllPokemons = false
                this.showPokemonByType = false
                return pokemons
            }
        },
    },
}
</script>

<style lang="scss" scoped>
@import './pokemon_types.scss';
@import url('https://fonts.googleapis.com/css2?family=Changa:wght@400;700&display=swap');
.header,
.main,
input[type='text'],
.searchButton {
    font-family: 'Changa', sans-serif;
}
.header,
input[type='text'],
.searchButton {
    font-size: 1.5rem;
}
.main {
    font-size: 1.2rem;
    background-color: $pokedex-green;
}
.header {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100px;
    background-color: $pokedex-red;
    color: white;
    & .searchButton {
        background-color: #1cb02b;
        color: white;
        border: none;
        margin-left: 10px;
        border-radius: 10px;
        cursor: pointer;
        &:hover {
            background-color: #1c8b0a;
        }
    }
    & input[type='text'] {
        border-radius: 10px;
        outline: none;
        border: none;
    }
}
.pokemonCard {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    align-content: center;
    & .nameImage,
    & .type,
    & .stats {
        width: 33%;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
    }
    & .nameImage {
        & .pokemonName {
            text-transform: capitalize;
        }
        & img {
            width: 200px;
            background-color: $pokedex-blue;
            border-radius: 50%;
        }
    }
    & .type li {
        width: 90%;
        margin-bottom: 10px;
        text-align: center;
        border-radius: 20px;
    }
    & .stats li {
        align-self: center;
    }
}
ul {
    padding: 0;
}
.type {
    & li {
        list-style: none;
        color: white;
        text-transform: uppercase;
    }
}

.stats {
    color: black;
    & li {
        list-style: none;
        text-transform: uppercase;
    }
}
.Normal {
    background-color: $normal;
}
.Fire {
    background-color: $fire;
}
.Water {
    background-color: $water;
}
.Grass {
    background-color: $grass;
}
.Electric {
    background-color: $electric;
}
.Ice {
    background-color: $ice;
}
.Fighting {
    background-color: $fighting;
}
.Poison {
    background-color: $poison;
}
.Ground {
    background-color: $ground;
}
.Flying {
    background-color: $flying;
}
.Psychic {
    background-color: $psychic;
}
.Bug {
    background-color: $bug;
}
.Rock {
    background-color: $rock;
}
.Ghost {
    background-color: $ghost;
}
.Dark {
    background-color: $dark;
}
.Dragon {
    background-color: $dragon;
}
.Steel {
    background-color: $steel;
}
.Fairy {
    background-color: $fairy;
}
@media screen and (max-width: 820px) {
    .header {
        flex-direction: column;
        height: 120px;
        & .searchButton {
            width: 70%;
            margin-top: 10px;
        }
    }
    .pokemonCard {
        flex-direction: column;
        align-items: center;
    }
}
@media screen and (max-width: 600px) {
    .header {
        font-size: 1rem;
        & input[type='text'] {
            font-size: 1rem;
        }
        & .searchButton {
            font-size: 1rem;
        }
    }
    .pokemonCard {
        & .stats {
            width: 90%;
        }
    }
}
@media screen and (max-width: 400px) {
    .header {
        & label {
            text-align: center;
        }
    }
}
</style>
