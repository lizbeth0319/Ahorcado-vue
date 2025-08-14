<template>
  <div class="game-screen" :style="{ backgroundImage: `url(${getBackground})` }">
    <div class="game-area">
      <div class="section hangman-section">
        <HangmanDisplay :errors="errors" :character="gameOptions.character" />
        <p class="error-counter">{{ translations[lang].errorsLeft }}: {{ gameOptions.maxErrors - errors }}</p>
      </div>

      <div class="section word-and-keyboard-section">
        <WordDisplay :word="currentWord" :guessedLetters="guessedLetters" />
        <Keyboard @letterGuessed="handleLetterGuess" :guessedLetters="guessedLetters" />
      </div>

      <div class="section game-info-section">
        <h2>{{ translations[lang].category }}: {{ getCategoryName(gameOptions.category) }}</h2>
        <p>{{ translations[lang].difficulty }}: {{ getDifficultyName(gameOptions.difficulty) }}</p>
      </div>
    </div>

    <GameOverModal
      v-if="gameOver"
      :won="won"
      :word="currentWord"
      @playAgain="resetGame"
      @returnToMenu="returnToMenu"
      :lang="lang"
    />

    <div class="advertisement-placeholder">
      <p>{{ translations[lang].adPlaceholder }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue';
import HangmanDisplay from './HangmanDisplay.vue';
import Keyboard from './Keyboard.vue';
import WordDisplay from './WordDisplay.vue';
import GameOverModal from './GameOverModal.vue';

import ninjaBg from '@/assets/ninja-bg.jpg';
import vaqueroBg from '@/assets/vaquero-bg.jpg';
import baseBg from '@/assets/base-bg.jpg';

const props = defineProps(['gameOptions', 'lang']);
const emit = defineEmits(['endGame']);


const wordList = {
  paises: { es: ['COLOMBIA', 'MEXICO', 'ESPAÑA', 'CHILE', 'ARGENTINA', 'PERU', 'BRASIL', 'ECUADOR', 'CANADA', 'JAPON'], en: ['COLOMBIA', 'MEXICO', 'SPAIN', 'CHILE', 'ARGENTINA', 'PERU', 'BRAZIL', 'ECUADOR', 'CANADA', 'JAPAN'] },
  frutas: { es: ['MANZANA', 'PLATANO', 'FRESA', 'UVA', 'NARANJA', 'PIÑA', 'KIWI', 'PERA', 'CEREZA', 'LIMON'], en: ['APPLE', 'BANANA', 'STRAWBERRY', 'GRAPE', 'ORANGE', 'PINEAPPLE', 'KIWI', 'PEAR', 'CHERRY', 'LEMON'] },
  profesiones: { es: ['MEDICO', 'INGENIERO', 'PROFESOR', 'POLICIA', 'ARQUITECTO', 'COCINERO', 'ARTISTA', 'PILOTO', 'PROGRAMADOR', 'DETECTIVE'], en: ['DOCTOR', 'ENGINEER', 'TEACHER', 'POLICE', 'ARCHITECT', 'CHEF', 'ARTIST', 'PILOT', 'PROGRAMMER', 'DETECTIVE'] },
  animales: { es: ['LEON', 'TIGRE', 'ELEFANTE', 'JIRAFA', 'MONO', 'OSO', 'PERRO', 'GATO', 'DELFIN', 'LOBO'], en: ['LION', 'TIGER', 'ELEPHANT', 'GIRAFFE', 'MONKEY', 'BEAR', 'DOG', 'CAT', 'DOLPHIN', 'WOLF'] },
  colores: { es: ['ROJO', 'AZUL', 'VERDE', 'AMARILLO', 'MORADO', 'NARANJA', 'BLANCO', 'NEGRO', 'ROSA', 'GRIS'], en: ['RED', 'BLUE', 'GREEN', 'YELLOW', 'PURPLE', 'ORANGE', 'WHITE', 'BLACK', 'PINK', 'GREY'] },
  comida: { es: ['PIZZA', 'HAMBURGUESA', 'PASTA', 'SOPA', 'ENSALADA', 'SUSHI', 'TACOS', 'POLLO', 'ARROZ', 'PESCADO'], en: ['PIZZA', 'HAMBURGER', 'PASTA', 'SOUP', 'SALAD', 'SUSHI', 'TACOS', 'CHICKEN', 'RICE', 'FISH'] },
  deportes: { es: ['FUTBOL', 'BALONCESTO', 'TENIS', 'NATACION', 'VOLEIBOL', 'RUGBY', 'CICLISMO', 'ATLETISMO', 'BOXEO', 'GOLF'], en: ['FOOTBALL', 'BASKETBALL', 'TENNIS', 'SWIMMING', 'VOLLEYBALL', 'RUGBY', 'CYCLING', 'ATHLETICS', 'BOXING', 'GOLF'] },
  marcas: { es: ['NIKE', 'ADIDAS', 'APPLE', 'SAMSUNG', 'COCACOLA', 'PEPSI', 'SONY', 'MICROSOFT', 'GOOGLE', 'BMW'], en: ['NIKE', 'ADIDAS', 'APPLE', 'SAMSUNG', 'COCACOLA', 'PEPSI', 'SONY', 'MICROSOFT', 'GOOGLE', 'BMW'] },
  peliculas: { es: ['TITANIC', 'AVATAR', 'MATRIX', 'GLADIADOR', 'ORIGEN', 'INTERESTELAR', 'CREPUSCULO', 'SPIDERMAN', 'PIRATAS', 'SHREK'], en: ['TITANIC', 'AVATAR', 'MATRIX', 'GLADIATOR', 'INCEPTION', 'INTERSTELLAR', 'TWILIGHT', 'SPIDERMAN', 'PIRATES', 'SHREK'] },
  musica: { es: ['JAZZ', 'POP', 'ROCK', 'SALSA', 'REGGAE', 'HIPHOP', 'CLASSIC', 'BLUES', 'COUNTRY', 'METAL'], en: ['JAZZ', 'POP', 'ROCK', 'SALSA', 'REGGAE', 'HIPHOP', 'CLASSIC', 'BLUES', 'COUNTRY', 'METAL'] },
};
const translations = {
  es: {
    errorsLeft: 'Errores restantes',
    category: 'Categoría',
    difficulty: 'Dificultad',
    adPlaceholder: '¡Tu anuncio va aquí!',
    paises: 'Países', frutas: 'Frutas', profesiones: 'Profesiones', animales: 'Animales', colores: 'Colores',
    comida: 'Comida', deportes: 'Deportes', marcas: 'Marcas', peliculas: 'Películas', musica: 'Música',
    facil: 'Fácil', normal: 'Normal', dificil: 'Difícil',
  },
  en: {
    errorsLeft: 'Errors remaining',
    category: 'Category',
    difficulty: 'Difficulty',
    adPlaceholder: 'Your ad goes here!',
    paises: 'Countries', frutas: 'Fruits', profesiones: 'Professions', animales: 'Animals', colores: 'Colors',
    comida: 'Food', deportes: 'Sports', marcas: 'Brands', peliculas: 'Movies', musica: 'Music',
    facil: 'Easy', normal: 'Normal', dificil: 'Hard',
  },
};

const currentWord = ref('');
const guessedLetters = ref(new Set());
const errors = ref(0);
const won = ref(false);
const gameOver = ref(false);

const getBackground = computed(() => {
  switch (props.gameOptions.character) {
    case 'ninja':
      return ninjaBg;
    case 'vaquero':
      return vaqueroBg;
    case 'base':
    default:
      return baseBg;
  }
});

const initializeGame = () => {
  const wordsForCategory = wordList[props.gameOptions.category][props.lang];
  currentWord.value = wordsForCategory[Math.floor(Math.random() * wordsForCategory.length)].toUpperCase();
  guessedLetters.value = new Set();
  errors.value = 0;
  won.value = false;
  gameOver.value = false;
};

const handleLetterGuess = (letter) => {
  if (gameOver.value) return;

  const upperCaseLetter = letter.toUpperCase();
  if (guessedLetters.value.has(upperCaseLetter)) {
    return;
  }

  guessedLetters.value.add(upperCaseLetter);

  if (!currentWord.value.includes(upperCaseLetter)) {
    errors.value++;
  }
};

const endGame = (hasWon) => {
  won.value = hasWon;
  gameOver.value = true;
};

const resetGame = () => {
  initializeGame();
};

const returnToMenu = () => {
  emit('endGame');
};

const getCategoryName = (id) => {
  return translations[props.lang][id] || id;
};

const getDifficultyName = (id) => {
  return translations[props.lang][id] || id;
};


onMounted(() => {
  initializeGame();
});

watch(errors, (newVal) => {
  if (newVal >= props.gameOptions.maxErrors) {
    endGame(false);
  }
});

watch(guessedLetters, () => {
  const allLettersGuessed = [...currentWord.value].every(letter =>
    guessedLetters.value.has(letter) || letter === ' '
  );
  if (allLettersGuessed) {
    endGame(true);
  }
}, { deep: true });

watch(() => props.gameOptions.category, () => {
  initializeGame();
});

watch(() => props.lang, () => {
  initializeGame();
});
</script>

<style scoped>
.game-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  padding: 30px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  max-width: 900px;
  width: 95%;
  position: relative;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  min-height: 500px;
}

.game-area {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 20px;
  width: 100%;
}

.section {
  padding: 15px;
  border-radius: 10px;
  background-color: rgba(240, 240, 240, 0.7);
  box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.1);
}

.hangman-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.error-counter {
  font-size: 1.1em;
  font-weight: bold;
  color: #c0392b;
  margin-top: 15px;
}

.word-and-keyboard-section {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  gap: 20px;
}

.game-info-section {
  text-align: left;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  gap: 10px;
}

.game-info-section h2, .game-info-section p {
  margin: 0;
  color: #333;
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

@media (max-width: 768px) {
  .game-area {
    grid-template-columns: 1fr;
  }
}
</style>
