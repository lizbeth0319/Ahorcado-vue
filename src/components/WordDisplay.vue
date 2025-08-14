<template>
  <div class="word-display">
    <span
      v-for="(char, index) in displayWord"
      :key="index"
      class="word-char"
    >
      {{ char === ' ' ? '&nbsp;' : (guessedLetters.has(char) ? char : '_') }}
    </span>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  word: {
    type: String,
    required: true,
  },
  guessedLetters: {
    type: Set,
    required: true,
  },
});

const displayWord = computed(() => {

  return props.word.split('');
});
</script>

<style scoped>
.word-display {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 20px;
  font-family: 'Courier New', monospace;
  font-size: 2.5em;
  font-weight: bold;
  letter-spacing: 5px;
  color: #333;
  flex-wrap: wrap;
}

.word-char {
  border-bottom: 3px solid #666;
  padding: 0 5px;
  min-width: 30px;
  text-align: center;
}
</style>
