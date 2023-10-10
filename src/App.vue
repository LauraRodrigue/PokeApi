<template>
  <div class="contenedorPadre">
    <!-- Navbar -->

    <nav class="navbar" style="background-color: #57C4E5;">
      <div class="container-fluid">
        <a class="navbar-brand">
          <img class="logo"
            src="https://res.cloudinary.com/dioxkbk6g/image/upload/v1569205776/Pokeapi/logo-6221638601ef7fa7c835eae08ef67a16_xokydx.png"
            alt="">
        </a>

        <form class="d-flex" role="search">
          <input v-model="criterioDeBusqueda" class="form-control me-2" type="search" placeholder="Search"
            aria-label="Search">
          <button @click.prevent="buscar()" class="btn btn-outline-success" type="submit">Buscar</button>
          <div class="dropdown">
            <select name="" id="" @change="filtrar()" v-model="tipof">
              <option :value="tipo" v-for="(tipo, i) in tiposDisponibles" :key="i">{{ tipo }}</option>
            </select>
          </div>
          <button @click="iniciar()" class="btn btn-outline-success" type="submit">Inicio</button>
        </form>
      </div>




    </nav>

    <!-- Conditional Content based on 'busqueda' -->
    <div class="container3" style="align-items: center" v-if="busqueda && respuesta.id">
      <div class="row row-cols-1 row-cols-md-4 g-3" style="width: 94%; text-align: center; margin: 6%">
        <div class="card text-center">
          <button @click="obtenerUrlPokemon(respuesta.url)">
            <img :src="respuesta.img" class="card-img-top" alt="...">
          </button>
          <div class="card-body">
            <h6>N°{{ respuesta.id }}</h6>
            <h3>{{ respuesta.nombre }}</h3>
            <div class="tipos">
              <button type="button" class="btn btn-primary" v-for="(tipo, i) in respuesta.tipos" :key="i"
                :style="{ backgroundColor: getColor(tipo) }" style="border: solid transparent; margin-left: 3%">
                {{ tipo }}
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

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

    <div class="container4" style="align-items: center" v-if="opcionSeleccionada">

      <div class="row row-cols-1 row-cols-md-4 g-3" style="width: 94%; text-align: center; margin: 6%">
        <div class="card text-center" v-for="(pokemon, index) in nuevo" :key="index">
          <button @click="obtenerUrlPokemon(pokemon.url)">
            <img :src="pokemon.img" class="card-img-top" alt="...">
          </button>
          <div class="card-body">
            <h6>N°{{ pokemon.id }}</h6>
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
    </div>




    <div class="container1" style="align-items: center" v-else-if="inicio">
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

  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

// Define reactive variables
let contenedor = ref([]);
let numActual = ref(0);
let hola = ref(false);
let busqueda = ref(false);
let inicio = ref(false);
let criterioDeBusqueda = ref('');
let respuesta = ref({});
const opcionSeleccionada = ref(false);
const nuevo = ref([]);
const componenteNuevo = ref(false);

const tiposDisponibles = [
  "fire", "water", "grass", "poison", "flying", "bug", "normal", "ground",
  "electric", "fairy"
];




// Function to search for a Pokemon
async function buscar() {
  try {
    let criterio = await axios.get(`https://pokeapi.co/api/v2/pokemon/${criterioDeBusqueda.value.toLowerCase()}`);
    let pokemonData = criterio.data;
    respuesta.value = {
      url: criterio.config.url,
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
    busqueda.value = true;// Asegúrate de establecer busqueda a true después de obtener la respuesta
    hola.value = false;
    inicio.value = false;
  } catch (error) {
    console.error(error);
  }
}

const tipof = ref("")

const filtrar = async () => {
  try {
    console.log("h");
    console.log(tipof.value)
    nuevo.value = [];
    let response = await axios.get(`https://pokeapi.co/api/v2/type/${tipof.value}`);
    let pokemonUrls = response.data.pokemon.map((poke) => poke.pokemon.url);

    await Promise.all(
      pokemonUrls.map(async (url) => {
        let pokemonResponse = await axios.get(url);
        let pokemonData = pokemonResponse.data;
        console.log(pokemonData)
        nuevo.value.push({
          id: pokemonData.id,
          img: pokemonData.sprites.other["official-artwork"].front_default,
          nombre: pokemonData.name,
          altura: pokemonData.height,
          peso: pokemonData.weight,
          tipos: pokemonData.types.map((tipo) => tipo.type.name),
          estadisticas: pokemonData.stats.map((stat) => {
            return { name: stat.stat.name, cant: stat.base_stat };
          }),
        });
      })
    );
    console.log(nuevo.value)
    componenteNuevo.value = true;
    hola.value = false;
    busqueda.value = false;
    inicio.value = false;
    opcionSeleccionada.value = true
  } catch (error) {
    console.error(error);
  }
}

// Function to load more Pokemon
async function iniciar() {
  busqueda.value = false;
  hola.value = false;
  inicio.value = true;
  try {
    contenedor.value = [];
    numActual.value += 50;
    let r = await axios.get(`https://pokeapi.co/api/v2/pokemon?limit=${numActual.value}&offset=0`);
    for (const result of r.data.results) {
      let c = await axios.get(result.url);
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
    }
  } catch (error) {
    console.error(error);
  }
}

// Function to get color based on Pokemon type
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
      return "black";
  }
}


// Function to handle click event on Pokemon card
async function obtenerUrlPokemon(url) {
  try {
    busqueda.value = false;
    hola.value = true;
    let r = await axios.get(url);
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
  } catch (error) {
    console.error(error);
  }
}

// Trigger the iniciar function when component is mounted
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

.container1,
.container3 {
  margin: 0;
  background-color: #212738;
}

.texto {
  width: 60%;
  height: 100%;
}

.primer {
  width: 100%;
  height: 60%;
}

.logo {
  width: 30%;
  height: 30%;
}
</style>
