<template>
  <v-app class="app-container">
    <v-container >
      <v-row >
        <v-col
          cols="2" 
          v-for="pokemon in filtered_pokemons"
          :key="pokemon.name"
        >
          <v-card>
            <v-container >
              <v-row class="mx-0 d-flex justify-center">
                <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`" :alt="pokemon.name" width="80%">
                <h2 class="text-center">{{get_name(pokemon)}}</h2>
              </v-row>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>

import axios from 'axios';
export default {
  name: "App",

  components: {},

  data(){
    return {
      pokemons: [],
      search:"",
      filterBy: "name",
      filterOptions: ["name", "type", "id", "species"]
    }
  },

  mounted(){
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((response) => {
      this.pokemons = response.data.results;
    })
  },
  methods: {
    get_id(pokemon){
      return pokemon.url.split("/")[6];
    },
    get_name(pokemon){
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    }
  },
  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        switch (this.filterBy) {
          case "name":
            return item.name.includes(this.search);
          case "type":
            return true; 
          case "id":
          if (this.search === "") {
            return true; 
          } else if (!isNaN(this.search)) {
            return parseInt(this.get_id(item)) === parseInt(this.search);
          }
          case "species":
            return true; 
          default:
            return true;
        }
      });
    }
  }
};



</script>

<style>
.app-container {
  background: linear-gradient(
      to bottom right,
      rgb(255, 0, 0),
      rgb(255, 255, 255)
    )
    no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
  color: rgb(0, 0, 0);
}


.custom-text-field,
.custom-select {
  color: #333; 
  background-color: #fff; 
}
</style>