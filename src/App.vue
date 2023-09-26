
<template>
  <div class="container1">
    <div>
      <h1>PokeApi</h1>
      <button @click="iniciar()">Cargar</button>
    </div>
    <div class="cuadricula">
        <div class="tarjeta" v-for="(pokemon, index) in contenedor" :key="index">
          <button>
            <img :src="pokemon.imagen" alt="">
          </button>
          <h6>#{{ pokemon.numero }}</h6>
          <h3>{{ pokemon.nombre }}</h3>
          <div class="tipos">
            <h6 class="tipo" v-for="(tipo, i) in pokemon.tipos" :key="i" :style="{backgroundColor:getColor(tipo)}">{{ tipo }}</h6>
          </div>
        </div>


    </div>

  </div>

  <!-- <div class="container2">
<button @click="obtenerUrlPokemon()">Peticion</button>
   <img :src="contenedor.imagen" alt="">
   <h1>#{{ contenedor.numero }}</h1>
   <h1>{{ contenedor.nombre }}</h1>
   <h2>Peso: {{ contenedor.peso }} KG</h2>
   <h5>Altura: {{ contenedor.altura }}</h5>
   <h6 v-for="(tipo, i) in contenedor.tipos" :key="i">{{ tipo }}</h6>
   <h3>Estadísticas:</h3>
   <div v-for="(stat, i) in contenedor.estadisticas" :key="i">
    <h6>{{ i }}:{{ stat }}</h6>
   </div>
   
</div> -->
</template>

<script setup>

import axios from "axios"
import { ref } from "vue"

let contenedor = ref([]);
let links = ref([]);

async function iniciar() {

  let r = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=50&offset=0");
  for (const result of r.data.results) {
    let c = await axios.get(result.url);
    let pokemonData = {
      numero: c.data.id,
      imagen: c.data.sprites.other['official-artwork'].front_default,
      nombre: c.data.name,
      peso: c.data.weight,
      altura: c.data.height,
      tipos: {
        tipo1: c.data.types[0].type.name,
        tipo2: c.data.types[1] ? c.data.types[1].type.name : null,
      },
      estadisticas: {
        Hp: c.data.stats[0].base_stat,
        Attack: c.data.stats[1].base_stat,
        Defense: c.data.stats[2].base_stat,
        Special_Attack: c.data.stats[3].base_stat,
        Special_Defense: c.data.stats[4].base_stat,
        Speed: c.data.stats[5].base_stat,
      },
    };
    contenedor.value.push(pokemonData);
    console.log(contenedor);
  };


}

function getColor(tipo){
  switch(tipo){
    case 'fire':
      return 'chocolate';
    case 'water':
      return 'dodgerblue';
    case 'grass':
      return 'green';
    case 'poison':
      return 'blueviolet';
    case 'flying':
      return 'pink';
    case 'bug':
      return 'aqua';
    case 'normal':
      return 'olive';
    case 'ground':
      return 'gray';
    case 'electric':
      return 'yellow';
    case 'fairy':
      return 'cornflowerblue'
    default:
      return 'white';
  }
}


async function obtenerUrlPokemon() {

  let r = await axios.get("https://pokeapi.co/api/v2/pokemon/25/");
  console.log(r);
  contenedor.value = {
    numero: r.data.id,
    imagen: r.data.sprites.other['official-artwork'].front_default,
    nombre: r.data.name,
    peso: r.data.weight,
    altura: r.data.height,
    tipos: {
      tipo1: r.data.types[0].type.name,
      tipo2: r.data.types[1] ? r.data.types[1].type.name : null,
    },
    estadisticas: {
      Hp: r.data.stats[0].base_stat,
      Attack: r.data.stats[1].base_stat,
      Defense: r.data.stats[2].base_stat,
      Special_Attack: r.data.stats[3].base_stat,
      Special_Defense: r.data.stats[4].base_stat,
      Speed: r.data.stats[5].base_stat,
    },

  }



}
</script>


<style scoped>
.tarjeta {
  border: 5px solid;
  width: 95%;
  height: 95%;
}

.cuadricula {
  width: 100%;
  display: grid;
  grid-template-columns: repeat(4, 25%);
}

img {
  width: 100%;
}
.tipos{
  display: flex;
  flex-direction: row;
  width: 100%;
}
.tipo{
  width: 40%;
  border-radius: 7%;
  margin-left: 7%;
}
</style>
