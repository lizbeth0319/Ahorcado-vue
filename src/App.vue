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

<script setup>
import { ref } from 'vue';
import HomeView from './components/HomeView.vue';
import GameView from './components/GameView.vue';


const currentView = ref('home');
const gameOptions = ref({});
const lang = ref('es');

const handleStartGame = (options) => {
  gameOptions.value = options;
  currentView.value = 'game';
};

const handleEndGame = () => {
  currentView.value = 'home';
};

const toggleLang = (newLang) => {
  lang.value = newLang;
};
</script>

<style>
#app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  background-image: url('https://images.unsplash.com/photo-1517400508544-738982390f77?q=80&w=1974&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D');
  background-size: cover;
  background-position: center;
  color: #fff;
}

.language-selector {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 100;
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
