<template>
  <v-app class="app-container">
    <v-container class="costum-container">
      <v-text-field
        v-model="search"
        label="Pesquisar"
        placeholder="Bulbasaur"
        solo
        class="costum-container"
      ></v-text-field>
      <v-select
        v-model="filterBy"
        :items="filterOptions"
        label="Filtrar por"
        style="margin-bottom:50px"
      ></v-select>
      <v-row >
        <v-col
          cols="2" 
          v-for="pokemon in filtered_pokemons"
          :key="pokemon.name"
        >
          <v-card @click="show_pokemon(get_id(pokemon))">
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

    <v-dialog v-model="show_dialog" width="800px">
      <v-card v-if="selected_pokemons">
        <v-container>
          <v-row class="d-flex align-center">
            <v-col cols=4>
              <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemons.id}.png`" :alt="selected_pokemons.name" width="80%">
            </v-col>
            <v-col cols=8>
              <h1>{{get_name(selected_pokemons)}}</h1>
              <v-chip 
              label 
              v-for="type in selected_pokemons.types" 
              :key="type.slot"
              class="mr-2"
              >{{ type.type.name }}</v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip label >
                <span>Altura {{selected_pokemons.height * 2.54}} cm</span>
              </v-chip>
              <v-chip label class="ml-2">
                <span>Peso {{(selected_pokemons.weight * 0.45359237).toFixed(0)}}kgs </span>
              </v-chip>
            </v-col>
          </v-row>
            {{ selected_pokemons }}
        </v-container>
      </v-card>
    </v-dialog>
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
      filterOptions: ["name", "type", "id", "species"],
      show_dialog: false,
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
    },
    show_pokemon(id){
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemons =  response.data;
        this.show_dialog = !this.show_dialog;
      })
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
        return false;
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
      rgb(25, 24, 24),
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

.costum-container{
  background-color:rgb(202, 202, 202);
}

</style>