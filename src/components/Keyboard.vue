<template>
  <div class="keyboard">
    <button
      v-for="letter in alphabet"
      :key="letter"
      @click="guessLetter(letter)"
      :disabled="guessedLetters.has(letter)"
    >
      {{ letter }}
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  guessedLetters: {
    type: Set,
    required: true,
  },
});

const emit = defineEmits(['letterGuessed']);

const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');

const guessLetter = (letter) => {
  emit('letterGuessed', letter);
};
</script>

<style scoped>
.keyboard {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(40px, 1fr));
  gap: 8px;
  padding: 15px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  margin-top: 20px;
}

.keyboard button {
  padding: 10px 5px;
  font-size: 1.1em;
  font-weight: bold;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.2s, opacity 0.2s;
  text-transform: uppercase;
  min-width: 40px;
}

.keyboard button:hover:not(:disabled) {
  background-color: #0056b3;
}

.keyboard button:disabled {
  background-color: #ccc;
  color: #888;
  cursor: not-allowed;
  opacity: 0.7;
}
</style>
