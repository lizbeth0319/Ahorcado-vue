<template>
  <div class="game-over-modal-overlay">
    <div class="game-over-modal-content">
      <h2 :class="{ 'win-text': won, 'lose-text': !won }">
        {{ message }}
      </h2>
      <p>
        {{ translations[lang].theWordWas }} <strong>"{{ word }}"</strong>
      </p>

      <div class="modal-actions">
        <button @click="$emit('playAgain')" class="play-again-button">
          {{ translations[lang].playAgainButton }}
        </button>
        <button @click="$emit('returnToMenu')" class="return-menu-button">
          {{ translations[lang].returnToMenuButton }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  won: {
    type: Boolean,
    required: true,
  },
  word: {
    type: String,
    required: true,
  },
  lang: {
    type: String,
    default: 'es',
    validator: (value) => ['es', 'en'].includes(value),
  },
});

const translations = {
  es: {
    winMessage: '¡Felicidades, ganaste!',
    loseMessage: '¡Lo siento, perdiste!',
    theWordWas: 'La palabra era:',
    playAgainButton: 'Jugar de Nuevo',
    returnToMenuButton: 'Volver al Menú',
  },
  en: {
    winMessage: 'Congratulations, you won!',
    loseMessage: 'Sorry, you lost!',
    theWordWas: 'The word was:',
    playAgainButton: 'Play Again',
    returnToMenuButton: 'Return to Menu',
  },
};

const message = computed(() => {
  return props.won ? translations[props.lang].winMessage : translations[props.lang].loseMessage;
});

</script>

<style scoped>
.game-over-modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.game-over-modal-content {
  background-color: white;
  padding: 40px;
  border-radius: 15px;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
  text-align: center;
  max-width: 500px;
  width: 90%;
  transform: scale(0.9);
  animation: modal-pop-in 0.3s forwards;
}

@keyframes modal-pop-in {
  from {
    transform: scale(0.7);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

h2 {
  font-size: 2.2em;
  margin-bottom: 15px;
}

.win-text {
  color: #28a745;
}

.lose-text {
  color: #dc3545;
}

p {
  font-size: 1.2em;
  color: #555;
  margin-bottom: 30px;
}

.modal-actions button {
  padding: 12px 25px;
  margin: 0 10px;
  border: none;
  border-radius: 8px;
  font-size: 1.1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.play-again-button {
  background-color: #007bff;
  color: white;
}

.play-again-button:hover {
  background-color: #0056b3;
}

.return-menu-button {
  background-color: #6c757d;
  color: white;
}

.return-menu-button:hover {
  background-color: #5a6268;
}
</style>
