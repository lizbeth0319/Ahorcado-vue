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

<script>
export default {
  name: 'WordDisplay',
  props: ['word', 'guessedLetters'],
  computed: {
    displayWord() {
      // Filtra caracteres que no son letras o espacios (ej. guiones, números si los hubiera en la palabra)
      // Y convierte la palabra a un array de caracteres para iterar
      return this.word.split('');
    }
  }
};
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