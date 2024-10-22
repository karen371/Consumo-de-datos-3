<script>
import axios from 'axios';
export default {
  data() {
    return {
      users: [],
      indicators: {},
      clima: {},
      pokemon: {},
    };
  },
  async mounted() {
    try {
      const userApi = "https://randomuser.me/api/?results=200"; // 200 usuarios
      const indicatorApi = "https://mindicador.cl/api/dolar/23-01-2024"; // Indicadores financieros
      const climaApi = "https://api.gael.cloud/general/public/clima/SCSE"; // Clima de una ciudad
      const pokeApi = "https://pokeapi.co/api/v2/pokemon/25"; // Pokémon Pikachu

      const [userResponse, indicatorResponse, climaResponse, pokeResponse] = await Promise.all([
        axios.get(userApi),
        axios.get(indicatorApi),
        axios.get(climaApi),
        axios.get(pokeApi),
      ]);

      this.users = userResponse.data.results; // Usuarios
      this.indicators = indicatorResponse.data; // Indicadores financieros
      this.clima = climaResponse.data; // Clima de Serena/Coquimbo
      this.pokemon = pokeResponse.data; // Pikachu

    } catch (error) {
      console.log('Error al obtener los datos de las APIs', error);
    }
  },
  computed: {
    datousuario() { // variable computada
      return this.users.map(user => ({
        nombre: `${user.name.first} ${user.name.last}`,
      }));
    },
    dolarValor() { // Computed para obtener el valor del dólar
      const dolarData = this.indicators.serie || [];
      const dolarEntry = dolarData.find(entry => entry.fecha.startsWith('2024-01-23'));
      return dolarEntry ? dolarEntry.valor : null;
    },
    climaInfo() {
      // Verificar si la información del clima se ha cargado
      if (this.clima) {
        return {
          descripcion: this.clima.Estado, 
          temperatura: this.clima.Temp, 
          humedad: this.clima.Humedad 
        };
      }
      return { descripcion: null, temperatura: null, humedad: null };
    },
    imgPokemon() {
      return this.pokemon.sprites ? this.pokemon.sprites.front_default : '';
    }
  }
};
</script>

<template>
  <div>
    <div>
      <h2>Ejercicio 1</h2>
      <div v-if="users.length > 0">
        <ol>
          <li v-for="(user, index) in datousuario" :key="index">
            {{ user.nombre }}
          </li>
        </ol>
      </div>
      <div v-else>
        <p>Cargando...</p>
      </div>
    </div>
    <div>
      <h2>Ejercicio 2</h2>
      <p>El valor del dólar el 23-01-2024</p>
      <p v-if="dolarValor"> 
        $ {{ dolarValor }} 
      </p>
      <p v-else>
        Cargando valor del dólar...
      </p>
    </div>
    <div>
      <h2>Ejercicio 3</h2>
      <p>El clima en este momento en Serena/Coquimbo es:</p>
      <p v-if="climaInfo.descripcion && climaInfo.temperatura">
        {{ climaInfo.descripcion }} con una temperatura de {{ climaInfo.temperatura }}°C
      </p>
      <p v-else>
        Cargando clima...
      </p>
    </div>
    <div>
      <h2>Ejercicio 4</h2>
      <div v-if="imgPokemon">
        <img :src="imgPokemon" alt="Pikachu" />
      </div>
      <div v-else>
        <p>Cargando información de Pikachu...</p>
      </div>
    </div>
  </div>
</template>


<style scoped>

</style>
