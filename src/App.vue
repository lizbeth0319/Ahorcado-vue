<template>
  <div id="app">
    <div class="language-selector">
      <button @click="toggleLang('es')" :class="{ 'active': lang === 'es' }">ES</button>
      <button @click="toggleLang('en')" :class="{ 'active': lang === 'en' }">EN</button>
    </div>

    <HomeView
      v-if="currentView === 'home'"
      @startGame="handleStartGame"
      :lang="lang"
    />
    <GameView 
      v-else-if="currentView === 'game'"
      :gameOptions="gameOptions"
      @endGame="handleEndGame"
      :lang="lang"
    />
  </div>
</template>

<script>
import HomeView from './components/HomeView.vue';
import GameView from './components/GameView.vue';

export default {
  name: 'App',
  components: {
    HomeView,
    GameView,
  },
  data() {
    return {
      currentView: 'home', // La vista inicial es HomeView
      gameOptions: {},     // Objeto para pasar las opciones de juego a GameView
      lang: 'es',          // Idioma por defecto
    };
  },
  methods: {
    handleStartGame(options) {
      this.gameOptions = options;
      this.currentView = 'game'; // Cambia a la vista del juego
    },
    handleEndGame() {
      this.currentView = 'home'; // Vuelve a la vista de inicio
    },
    toggleLang(newLang) {
      this.lang = newLang; // Cambia el idioma
    }
  },
};
</script>

<style>
.language-selector {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 100; /* Asegura que esté por encima de otros elementos */
}

.language-selector button {
  padding: 8px 12px;
  margin-left: 5px;
  border: 1px solid #fff;
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.2);
  color: white;
  cursor: pointer;
  transition: background-color 0.3s, border-color 0.3s;
}

.language-selector button.active {
  background-color: #fff;
  color: #007bff;
  font-weight: bold;
}

.language-selector button:hover:not(.active) {
  background-color: rgba(255, 255, 255, 0.4);
}
</style>