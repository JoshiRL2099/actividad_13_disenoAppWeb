<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';

const nombrePokemon = ref('');
const pokemon = ref<any>(null);
const error = ref('');
const cargando = ref(false);

const obtenerPokemon = async () => {
  pokemon.value = null;
  error.value = '';

  if (nombrePokemon.value.trim() === '') {
    error.value = 'Ingresa el nombre de un Pokémon';
    return;
  }

  try {
    cargando.value = true;

    const response = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${nombrePokemon.value.toLowerCase().trim()}`
    );

    pokemon.value = response.data;
  } catch (e) {
    error.value = 'Pokémon no encontrado, revisa el nombre e inténtalo de nuevo';
  } finally {
    cargando.value = false;
  }
};
</script>

<template>
  <main class="contenedor">
    <section class="tarjeta">
      <h1>Buscador de Pokémon</h1>

      <form @submit.prevent="obtenerPokemon" class="formulario">
        <label for="pokemon">Nombre del Pokémon</label>

        <input
          id="pokemon"
          type="text"
          v-model="nombrePokemon"
          placeholder="Ejemplo: pikachu"
        />

        <button type="submit">
          Buscar Pokémon
        </button>
      </form>

      <p v-if="cargando" class="mensaje">
        Buscando Pokémon...
      </p>

      <p v-if="error" class="error">
        {{ error }}
      </p>

      <div v-if="pokemon" class="resultado">
        <h2>{{ pokemon.name }}</h2>

        <img
          :src="pokemon.sprites.other['official-artwork'].front_default"
          :alt="pokemon.name"
          class="imagen-pokemon"
        />

        <h3>Rugido del Pokémon</h3>

        <audio
          v-if="pokemon.cries && pokemon.cries.latest"
          controls
          :src="pokemon.cries.latest"
        ></audio>

        <p v-else class="mensaje">
          Este Pokémon no tiene audio disponible
        </p>
      </div>
    </section>
  </main>
</template>

<style scoped>
.contenedor {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #00000000;
  font-family: Arial, sans-serif;
}

.tarjeta {
  width: 90%;
  max-width: 420px;
  padding: 30px;
  background-color: white;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.15);
  text-align: center;
}

h1 {
  color: #2c3e50;
  margin-bottom: 25px;
}

.formulario {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

label {
  font-weight: bold;
  color: #333;
}

input {
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
  text-align: center;
}

button {
  padding: 12px;
  border: none;
  border-radius: 8px;
  background-color: #ffcb05;
  color: #2c3e50;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background-color: #f2b807;
}

.resultado {
  margin-top: 25px;
}

.resultado h2 {
  text-transform: capitalize;
  color: #34495e;
}

.imagen-pokemon {
  width: 220px;
  max-width: 100%;
}

.error {
  margin-top: 20px;
  color: red;
  font-weight: bold;
}

.mensaje {
  margin-top: 20px;
  color: #555;
}

audio {
  width: 100%;
  margin-top: 10px;
}
</style>