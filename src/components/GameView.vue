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

    <div class="advertisement-placeholder">   <!-- esto tambien es para el anuncio  asi que no me lo borres pls-->
      <p>{{ translations[lang].adPlaceholder }}</p>
    </div>
  </div>
</template>

<script>
import HangmanDisplay from './HangmanDisplay.vue';
import Keyboard from './Keyboard.vue';
import WordDisplay from './WordDisplay.vue';
import GameOverModal from './GameOverModal.vue';

// IMAGENES DE FONDO, las ando llamando de assests, si no carga un fondo es porque las imagnes no se subieron bien
import ninjaBg from '@/assets/ninja-bg.jpg';
import vaqueroBg from '@/assets/vaquero-bg.jpg';
import baseBg from '@/assets/base-bg.jpg';

export default {
  name: 'GameView',
  components: {
    HangmanDisplay,
    Keyboard,
    WordDisplay,
    GameOverModal,
  },
  props: ['gameOptions', 'lang'],
  data() {
    return {
      wordList: {
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
      },
      currentWord: '',
      guessedLetters: new Set(),
      errors: 0,
      won: false,
      gameOver: false,
      translations: {
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
      },
    };
  },
  computed: {
    getBackground() {
      switch (this.gameOptions.character) {
        case 'ninja':
          return ninjaBg;
        case 'vaquero':
          return vaqueroBg;
        case 'base':
        default:
          return baseBg;
      }
    }
  },
  watch: {
    errors(newVal) {
      if (newVal >= this.gameOptions.maxErrors) {
        this.endGame(false);
      }
    },
    guessedLetters: {
      handler() {
        if (this.currentWord) {
          const allLettersGuessed = [...this.currentWord].every(letter =>
            this.guessedLetters.has(letter) || letter === ' '
          );
          if (allLettersGuessed) {
            this.endGame(true);
          }
        }
      },
      deep: true,
    },
    'gameOptions.category': {
      immediate: true,
      handler() {
        this.initializeGame();
      }
    },
    lang: {
        handler() {
            this.initializeGame();
        }
    }
  },
  methods: {
    initializeGame() {
      const wordsForCategory = this.wordList[this.gameOptions.category][this.lang];
      this.currentWord = wordsForCategory[Math.floor(Math.random() * wordsForCategory.length)].toUpperCase();
      this.guessedLetters = new Set();
      this.errors = 0;
      this.won = false;
      this.gameOver = false;
    },
    handleLetterGuess(letter) {
      if (this.gameOver) return;

      const upperCaseLetter = letter.toUpperCase();
      if (this.guessedLetters.has(upperCaseLetter)) {
        return;
      }

      this.guessedLetters.add(upperCaseLetter);

      if (!this.currentWord.includes(upperCaseLetter)) {
        this.errors++;
      }
    },
    endGame(hasWon) {
      this.won = hasWon;
      this.gameOver = true;
    },
    resetGame() {
      this.initializeGame();
    },
    returnToMenu() {
      this.$emit('endGame');
    },
    getCategoryName(id) {
        return this.translations[this.lang][id] || id;
    },
    getDifficultyName(id) {
        return this.translations[this.lang][id] || id;
    }
  },
  created() {
  },
};
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