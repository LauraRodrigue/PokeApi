

<template>
<div class="container">
<button @click="obtenerUrlPokemon()">Peticion</button>
   <img :src="contenedor.imagen" alt="">
   <h1>#{{ contenedor.numero }}</h1>
   <h1>{{ contenedor.nombre }}</h1>
   <h2>Peso: {{ contenedor.peso }} KG</h2>
   <h5>Altura: {{ contenedor.altura }}</h5>
   <h6>{{ contenedor.tipos.tipo1 }}</h6>
   <h6>{{ contenedor.tipos.tipo2 }}</h6>
   <h3>Estadísticas:</h3>
   <div v-for="(stat, i) in contenedor.estadisticas" :key="i">
    <h6>{{ i }} {{ stat }}</h6>
   </div>
   
</div>

</template>

<script setup>

import axios from "axios"
import {ref} from "vue"

let contenedor = ref({
  numero: "",
  imagen: "",
  nombre: "",
  peso: "",
  altura: "",
  tipos: {
    tipo1: "",
    tipo2: "",
  },
  estadisticas: {
    Hp: "",
    Attack: "",
    Defense: "",
    Special: "",
  },
});



 async function obtenerUrlPokemon(){
  /* let r = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=50&offset=0")
  console.log(r); */
  let r = await axios.get("https://pokeapi.co/api/v2/pokemon/25/");
   console.log(r);
   contenedor.value = {
    numero : r.data.id,
    imagen : r.data.sprites.other['official-artwork'].front_default,
    nombre : r.data.name,
    peso : r.data.weight,
    altura : r.data.height,
    tipos : {
      tipo1 : r.data.types[0].type.name, 
      tipo2 : r.data.types[1] ? r.data.types[1].type.name : null,
    },
    estadisticas : {
      Hp : r.data.stats[0].base_stat,
      Attack : r.data.stats[1].base_stat,
      Defense : r.data.stats[2].base_stat,
      Special : r.data.stats[3].base_stat,
    },
  
 }
  
  

 }
</script>


<style scoped>

</style>
