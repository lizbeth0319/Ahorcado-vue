<template>
  <div class="home-screen">
    <h1>{{ translations[lang].title }}</h1>

    <div class="option-group">
      <label>{{ translations[lang].categoryLabel }}</label>
      <select v-model="selectedCategory">
        <option v-for="category in categories" :key="category.id" :value="category.id">
          {{ category.name[lang] }}
        </option>
      </select>
    </div>

    <div class="option-group">
      <label>{{ translations[lang].difficultyLabel }}</label>
      <div class="difficulty-options">
        <button
          v-for="difficulty in difficulties"
          :key="difficulty.id"
          @click="selectedDifficulty = difficulty.id"
          :class="{ 'active': selectedDifficulty === difficulty.id }"
        >
          {{ difficulty.name[lang] }}
        </button>
      </div>
    </div>

    <div class="option-group">
      <label>{{ translations[lang].characterLabel }}</label>
      <div class="character-options">
        <div
          v-for="character in characters"
          :key="character.id"
          class="character-card"
          :class="{ 'active': selectedCharacter === character.id }"
          @click="selectedCharacter = character.id"
        >
          <CharacterSVG :type="character.id" />
          <span>{{ character.name[lang] }}</span>
        </div>
      </div>
    </div>

    <button @click="startGame" class="start-button">{{ translations[lang].startButton }}</button>

    <div class="advertisement-placeholder">
      <p>{{ translations[lang].adPlaceholder }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue';

const props = defineProps({
  lang: {
    type: String,
    required: true,
  },
});
const emit = defineEmits(['startGame']);


const CharacterSVG = {
  props: ['type'],
  template: `
    <div class="character-svg-container">
      <svg v-if="type === 'ninja'" viewBox="0 0 100 100" class="character-svg ninja">
        <circle cx="50" cy="40" r="20" fill="#333"/> <rect x="35" y="55" width="30" height="20" rx="5" ry="5" fill="#333"/> <path d="M50 50 L30 50 L25 55 L30 60 L50 60 Z" fill="#555"/> <path d="M50 50 L70 50 L75 55 L70 60 L50 60 Z" fill="#555"/> <path d="M45 75 L35 90 L65 90 L55 75 Z" fill="#333"/> <polygon points="50,20 60,30 40,30" fill="#666"/> </svg>
      <svg v-else-if="type === 'base'" viewBox="0 0 100 100" class="character-svg base">
        <circle cx="50" cy="30" r="25" fill="#f0c080"/> <rect x="40" y="55" width="20" height="30" fill="#6a11cb"/> <rect x="30" y="55" width="10" height="20" fill="#555"/> <rect x="60" y="55" width="10" height="20" fill="#555"/> <rect x="40" y="85" width="10" height="15" fill="#444"/> <rect x="50" y="85" width="10" height="15" fill="#444"/> </svg>
      <svg v-else-if="type === 'vaquero'" viewBox="0 0 100 100" class="character-svg vaquero">
        <circle cx="50" cy="35" r="20" fill="#d0a060"/> <path d="M30 30 Q50 15 70 30 L65 35 L35 35 Z" fill="#8B4513"/> <rect x="40" y="55" width="20" height="30" fill="#A0522D"/> <rect x="30" y="55" width="10" height="20" fill="#555"/> <rect x="60" y="55" width="10" height="20" fill="#555"/> <rect x="40" y="85" width="10" height="15" fill="#444"/> <rect x="50" y="85" width="10" height="15" fill="#444"/> </svg>
    </div>
  `,
};


const selectedCategory = ref('paises');
const selectedDifficulty = ref('normal');
const selectedCharacter = ref('base');

const categories = [
  { id: 'paises', name: { es: 'Países', en: 'Countries' } },
  { id: 'frutas', name: { es: 'Frutas', en: 'Fruits' } },
  { id: 'profesiones', name: { es: 'Profesiones', en: 'Professions' } },
  { id: 'animales', name: { es: 'Animales', en: 'Animals' } },
  { id: 'colores', name: { es: 'Colores', en: 'Colors' } },
  { id: 'comida', name: { es: 'Comida', en: 'Food' } },
  { id: 'deportes', name: { es: 'Deportes', en: 'Sports' } },
  { id: 'marcas', name: { es: 'Marcas', en: 'Brands' } },
  { id: 'peliculas', name: { es: 'Películas', en: 'Movies' } },
  { id: 'musica', name: { es: 'Música', en: 'Music' } },
];
const difficulties = [
  { id: 'facil', name: { es: 'Fácil (8 errores)', en: 'Easy (8 errors)' }, maxErrors: 8 },
  { id: 'normal', name: { es: 'Normal (6 errores)', en: 'Normal (6 errors)' }, maxErrors: 6 },
  { id: 'dificil', name: { es: 'Difícil (4 errores)', en: 'Hard (4 errors)' }, maxErrors: 4 },
];
const characters = [
  { id: 'ninja', name: { es: 'Ninja', en: 'Ninja' } },
  { id: 'base', name: { es: 'Base', en: 'Base' } },
  { id: 'vaquero', name: { es: 'Vaquero', en: 'Cowboy' } },
];
const translations = {
  es: {
    title: 'Juego del Ahorcado',
    categoryLabel: 'Elige una categoría:',
    difficultyLabel: 'Elige la dificultad:',
    characterLabel: 'Elige tu personaje:',
    startButton: '¡Empezar a Jugar!',
    adPlaceholder: '¡!',
  },
  en: {
    title: 'Hangman Game',
    categoryLabel: 'Choose a Category:',
    difficultyLabel: 'Choose Difficulty:',
    characterLabel: 'Choose your character:',
    startButton: 'Start Game!',
    adPlaceholder: 'Your ad goes here!',
  },
};

const startGame = () => {
  const selectedDifficultyObj = difficulties.find(d => d.id === selectedDifficulty.value);
  emit('startGame', {
    category: selectedCategory.value,
    difficulty: selectedDifficulty.value,
    maxErrors: selectedDifficultyObj ? selectedDifficultyObj.maxErrors : 6,
    character: selectedCharacter.value,
  });
};
</script>

<style scoped>
.home-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  padding: 30px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  max-width: 600px;
  width: 90%;
}

h1 {
  color: #333;
  margin-bottom: 20px;
}

.option-group {
  width: 100%;
  text-align: left;
}

.option-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  color: #555;
}

select {
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
  background-color: white;
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background-image: url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20viewBox%3D%220%200%2020%2020%22%3E%3Cpath%20fill%3D%22%23666%22%20d%3D%22M10%2014L4%207h12z%22%2F%3E%3C%2Fsvg%3E');
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 10px;
}

.difficulty-options button {
  padding: 10px 15px;
  margin-right: 10px;
  border: 1px solid #007bff;
  border-radius: 5px;
  background-color: white;
  color: #007bff;
  cursor: pointer;
  font-size: 15px;
  transition: background-color 0.3s, color 0.3s;
}

.difficulty-options button.active,
.difficulty-options button:hover {
  background-color: #007bff;
  color: white;
}

.character-options {
  display: flex;
  justify-content: space-around;
  width: 100%;
  gap: 15px;
  flex-wrap: wrap;
}

.character-card {
  border: 2px solid #ddd;
  border-radius: 10px;
  padding: 15px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  transition: all 0.3s ease;
  flex: 1 1 120px;
  max-width: 150px;
  background-color: #f9f9f9;
}

.character-card.active {
  border-color: #28a745;
  box-shadow: 0 0 10px rgba(40, 167, 69, 0.5);
  transform: translateY(-5px);
}

.character-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.character-svg-container {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background-color: #eee;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.character-svg {
  width: 100%;
  height: 100%;
  display: block;
}

.start-button {
  padding: 15px 30px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1.2em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.start-button:hover {
  background-color: #218838;
}

.advertisement-placeholder {
  width: 100%;
  padding: 15px;
  background-color: #f0f0f0;
  border: 1px dashed #ccc;
  border-radius: 8px;
  margin-top: 20px;
  text-align: center;
  color: #666;
}
</style>
