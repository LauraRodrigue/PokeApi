
<template>
  <div class="container2" v-if="hola">
    <div class="texto">
      <div class="primer">
        <h1>#{{ contenedor.numero }}</h1>
        <h1>{{ contenedor.nombre }}</h1>
        <h2>Peso: {{ contenedor.peso }} KG</h2>
        <h5>Altura: {{ contenedor.altura }}</h5>
        <h6 v-for="(tipo, i) in contenedor.tipos" :key="i">{{ tipo }}</h6>
      </div>
      <div class="segundo">
        <h3>Estadísticas:</h3>
        <div v-for="(stat, i) in contenedor.estadisticas" :key="i">
          <h6>{{ i }}:{{ stat }}</h6>
        </div>
      </div>
    </div>
    <div>
      <img class="imgG" :src="contenedor.imagen" alt="" />
    </div>
  </div>


  <div class="container3" style="align-items: center" v-if="busqueda">
    <nav class="navbar" style="background-color: #57C4E5;">
      <div class="container-fluid">
        <a class="navbar-brand">Navbar</a>
        <form class="d-flex" role="search">
          <input  v-model="criterioDeBusqueda" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
          <button @click="buscar()" class="btn btn-outline-success" type="submit">Search</button>
        </form>
      </div>
    </nav>
    <div>
      <h1>PokeApi</h1>
    </div>
    <div class="row row-cols-1 row-cols-md-4 g-3" style="width: 94%; text-align: center; margin: 6%">
      <div class="card" style="width: 18rem; margin: 1%" v-for="(pokemon, index) in contenedor" :key="index">
        <button @click="obtenerUrlPokemon(pokemon.url)">
          <img :src="pokemon.imagen" alt="" />
        </button>
        <div class="card-body">
          <h6>N°{{ pokemon.numero }}</h6>
          <h3>{{ pokemon.nombre }}</h3>
          <div class="tipos">
            <button type="button" class="btn btn-primary" v-for="(tipo, i) in pokemon.tipos" :key="i"
              :style="{ backgroundColor: getColor(tipo) }" style="border: solid transparent; margin-left: 3%">
              {{ tipo }}
            </button>
          </div>
        </div>
      </div>
    </div>
    <button type="button" class="btn btn-primary" @click="iniciar()">
      Ver más
    </button>
  </div>


  <div class="container1" style="align-items: center" v-else>
    <nav class="navbar" style="background-color: #57C4E5;">
      <div class="container-fluid">
        <a class="navbar-brand">Navbar</a>
        <form class="d-flex" role="search">
          <input  v-model="criterioDeBusqueda" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
          <button @click="buscar()" class="btn btn-outline-success" type="submit">Search</button>
        </form>
      </div>
    </nav>
    <div>
      <h1>PokeApi</h1>
    </div>
    <div class="row row-cols-1 row-cols-md-4 g-3" style="width: 94%; text-align: center; margin: 6%">
      <div class="card" style="width: 18rem; margin: 1%" v-for="(pokemon, index) in contenedor" :key="index">
        <button @click="obtenerUrlPokemon(pokemon.url)">
          <img :src="pokemon.imagen" alt="" />
        </button>
        <div class="card-body">
          <h6>N°{{ pokemon.numero }}</h6>
          <h3>{{ pokemon.nombre }}</h3>
          <div class="tipos">
            <button type="button" class="btn btn-primary" v-for="(tipo, i) in pokemon.tipos" :key="i"
              :style="{ backgroundColor: getColor(tipo) }" style="border: solid transparent; margin-left: 3%">
              {{ tipo }}
            </button>
          </div>
        </div>
      </div>
    </div>
    <button type="button" class="btn btn-primary" @click="iniciar()">
      Ver más
    </button>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";

let contenedor = ref([]);
let numActual = ref(0);
let hola = ref(false);
let busqueda = ref(false);
let criterioDeBusqueda = ref('')
let respuesta = ref({});

 async function buscar() {
  hola.value = false;
  busqueda.value = true;
  let criterio = await axios.get(`https://pokeapi.co/api/v2/pokemon/${criterioDeBusqueda.value.toLowerCase()}`);

  let pokemonData = criterio.data;
    respuesta.value = {
    id: pokemonData.id,
    img: pokemonData.sprites.other["official-artwork"].front_default,
    nombre: pokemonData.name,
    altura: pokemonData.height,
    peso: pokemonData.weight,
    tipos: pokemonData.types.map((tipo) => tipo.type.name),
    estadisticas: pokemonData.stats.map((stat) => {
      return { name: stat.stat.name, cant: stat.base_stat };
    }),
  };
}

async function iniciar() {
  contenedor.value = [];
  numActual.value += 50;
  let r = await axios.get(
    `https://pokeapi.co/api/v2/pokemon?limit=${numActual.value}&offset=0`
  );

  for (const result of r.data.results) {
    let c = await axios.get(result.url);
    console.log(c);
    let pokemonData = {
      url: result.url,
      numero: c.data.id,
      imagen: c.data.sprites.other["official-artwork"].front_default,
      nombre: c.data.name,
      peso: c.data.weight,
      altura: c.data.height,
      tipos: c.data.types.map((tipo) => tipo.type.name),
      estadisticas: c.data.stats.map((stat) => {
      return { name: stat.stat.name, cant: stat.base_stat };
    }),
    };
    contenedor.value.push(pokemonData);
    console.log(contenedor);
  }
}




function getColor(tipo) {
  switch (tipo) {
    case "fire":
      return "chocolate";
    case "water":
      return "dodgerblue";
    case "grass":
      return "green";
    case "poison":
      return "blueviolet";
    case "flying":
      return "pink";
    case "bug":
      return "aqua";
    case "normal":
      return "olive";
    case "ground":
      return "gray";
    case "electric":
      return "yellow";
    case "fairy":
      return "cornflowerblue";
    default:
      return "white";
  }
}

async function obtenerUrlPokemon(url) {
  hola.value = true;
  busqueda.value = false;
  let r = await axios.get(url);
  console.log(r);
  contenedor.value = {
    numero: r.data.id,
    imagen: r.data.sprites.other["official-artwork"].front_default,
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
  };
}

import { onMounted } from 'vue';
onMounted(iniciar);
</script>


<style scoped>
img {
  width: 100%;
}

.tipos {
  display: flex;
  flex-direction: row;
  width: 100%;
}

.row {
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
}

.imgG {
  height: 500px;
  width: 500px;
}

.container2 {
  display: flex;
  flex-direction: row;
  background-color: #A2DAFF;
  height: 100vh;
}

.container1 {
  background-color: #212738;
}

.texto {
  width: 60%;
  height: 100%;
}

.primer {
  width: 100%;
  height: 60%;
}</style>
