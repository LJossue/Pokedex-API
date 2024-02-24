<template>
  <div>
    <header>
      <nav class="nav">
        <img
          src="../assets/logo.png"
          alt="Logo Pokédex"
          class="logo fade-in-up"
        />
        <ul class="nav-list">
          <li class="nav-item">
            <button
              class="btn btn-header fade-in"
              @click="filterPokemon('ver-todos')"
            >
              Ver todos
            </button>
          </li>
          <li class="nav-item" v-for="type in types" :key="type">
            <button
              class="btn btn-header fade-in"
              :class="['btn-header', type.toLocaleLowerCase()]"
              @click="filterPokemon(type)"
            >
              {{ type }}
            </button>
          </li>
        </ul>
      </nav>
    </header>
    <div id="todos">
      <div id="listaPokemon" class="pokemon-todos">
        <div
          v-for="pokemon in filteredPokemons"
          :key="pokemon.id"
          class="pokemon"
        >
          <p class="pokemon-id-back">#{{ pokemon.id }}</p>
          <div class="pokemon-imagen">
            <img
              :src="pokemon.sprite"
              :alt="pokemon.name"
              @mouseover="changeSprite(pokemon, true)"
              @mouseleave="changeSprite(pokemon, false)"
            />
          </div>
          <div class="pokemon-info">
            <div class="nombre-contenedor">
              <p class="pokemon-id">#{{ pokemon.id }}</p>
              <h2 class="pokemon-nombre">{{ pokemon.name }}</h2>
            </div>
            <div class="pokemon-tipos">
              <p
                v-for="(type, index) in pokemon.types"
                :key="index"
                :class="[type.toLowerCase(), 'tipo']"
              >
                {{ type.toUpperCase() }}
              </p>
            </div>
            <div class="pokemon-stats">
              <p class="stat">{{ pokemon.height }}</p>
              <p class="stat">{{ pokemon.weight }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const pokemons = ref([]);
const filteredPokemons = ref([]);
const types = [
  "Normal",
  "Fire",
  "Water",
  "Grass",
  "Electric",
  "Ice",
  "Fighting",
  "Poison",
  "Ground",
  "Flying",
  "Psychic",
  "Bug",
  "Rock",
  "Ghost",
  "Dark",
  "Dragon",
  "Steel",
  "Fairy",
];

onMounted(async () => {
  const URL = "https://pokeapi.co/api/v2/pokemon/";
  for (let i = 1; i <= 151; i++) {
    const response = await fetch(URL + i);
    const data = await response.json();
    const formattedId = String(data.id).padStart(3, "0");
    const pokemon = {
      id: formattedId,
      name: data.name,
      sprite: data.sprites.front_default, // Cambiado a sprite (antes spriteFront)
      spriteFront: data.sprites.front_default,
      spriteBack: data.sprites.back_default,
      types: data.types.map((type) => type.type.name),
      height: data.height,
      weight: data.weight,
    };
    pokemons.value.push(pokemon);
  }
  filteredPokemons.value = pokemons.value;
});

function changeSprite(pokemon, isHovering) {
  if (isHovering) {
    pokemon.sprite = pokemon.spriteBack; // Usar spriteBack en lugar de sprites.back_default
  } else {
    pokemon.sprite = pokemon.spriteFront; // Usar spriteFront en lugar de sprites.front_default
  }
}
function filterPokemon(type) {
  if (type === "ver-todos") {
    filteredPokemons.value = pokemons.value;
  } else {
    filteredPokemons.value = pokemons.value.filter((pokemon) =>
      pokemon.types.includes(type.toLowerCase())
    );
  }
}
</script>

<style scoped>
.logo {
  width: 100%;
  max-width: 20rem;
  display: flex;
  justify-content: center;
  text-align: center;
  margin: auto;
  filter: drop-shadow(0 0 1rem rgba(168, 168, 168, 0.25));
  opacity: 0; /* Inicialmente ocultar el logo */
  transform: translateY(2rem); /* Desplazar hacia abajo 2rem */
}

.fade-in-up {
  animation: fadeInUpAnimation 1s ease forwards; /* Animación de entrada */
}

@keyframes fadeInUpAnimation {
  from {
    opacity: 0; /* Empezar con opacidad 0 */
    transform: translateY(2rem); /* Empezar con desplazamiento hacia abajo */
  }
  to {
    opacity: 1; /* Terminar con opacidad 1 */
    transform: translateY(0); /* Terminar sin desplazamiento */
  }
}

header {
  padding-block: 1rem;
  box-shadow: 0 0 2rem -1rem rgba(233, 233, 233, 0.5);
}
.nav {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: flex-start;
  padding-inline: 2rem;
  max-width: 1000px;
  margin: 0 auto;
}
.nav-list {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}
.btn-header {
  padding: 0.5rem;
  border-radius: 100vmax;
  cursor: pointer;
  text-transform: uppercase;
  font-weight: 600;
  box-shadow: 0 0 1rem rgba(168, 168, 168, 0.25);
  transition: all 0.3s ease;
  opacity: 0;
}
.fade-in {
  animation: fadeInAnimation 1s ease forwards; /* Animación de entrada */
}

@keyframes fadeInAnimation {
  from {
    opacity: 0; /* Empezar con opacidad 0 */
  }
  to {
    opacity: 1; /* Terminar con opacidad 1 */
  }
}
.btn-header:hover {
  transform: scale(1.1);
  box-shadow: 0 0 2rem rgba(168, 168, 168, 0.25);
}

#ver-todos {
  background-color: var(--clr-gray);
}
.pokemon-todos {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}
@media screen and (min-width: 470px) {
  .pokemon-todos {
    grid-template-columns: 1fr 1fr;
  }
}
@media screen and (min-width: 700px) {
  .pokemon-todos {
    grid-template-columns: 1fr 1fr 1fr;
  }
}
.pokemon {
  border-radius: 1rem;
  background-color: var(--clr-black);
  box-shadow: 0 0 3rem -1rem rgba(131, 131, 131, 0.25);
  padding-block: 1rem;
  text-transform: uppercase;
  position: relative;
  isolation: isolate;
  overflow: hidden;
}
.pokemon-id-back {
  position: absolute;
  top: 1rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 6rem;
  font-weight: 800;
  color: var(--clr-gray);
  z-index: -1;
}
.pokemon-imagen {
  padding-inline: 1rem;
  display: flex;
  justify-content: center;
}
.pokemon-imagen img {
  width: 100%;
  max-width: 8rem;
}
.pokemon-info {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding-inline: 1rem;
  align-items: center;
  text-align: center;
}
.nombre-contenedor {
  display: flex;
  align-items: center;
  column-gap: 0.5rem;
  flex-wrap: wrap;
  justify-content: center;
}
.pokemon-id {
  background-color: var(--clr-gray);
  padding: 0.25 0.5rem;
  border-radius: 100vmax;
  font-size: 0.75rem;
  font-weight: 500;
}
.pokemon-nombre {
  font-size: 1.4rem;
}
.pokemon-tipos {
  display: flex;
  gap: 0.5rem;
  font-size: 0.75rem;
  font-weight: 500;
  flex-wrap: wrap;
  justify-content: center;
}
.tipo {
  padding: 0.25rem 0.5rem;
  border-radius: 100vmax;
}
.pokemon-stats {
  display: flex;
  gap: 1rem;
  font-size: 0.85rem;
}
.stat {
  background-color: var(--clr-gray);
  padding: 0.25rem 0.5rem;
  border-radius: 100vmax;
}
</style>
