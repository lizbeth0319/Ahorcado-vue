<template>
  <div class="hangman-container">
    <canvas ref="hangmanCanvas" width="200" height="250"></canvas>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';

const props = defineProps({
  errors: {
    type: Number,
    required: true,
  },
  character: {
    type: String,
    required: true,
  },
});


const hangmanCanvas = ref(null);

const drawHangman = () => {
  const canvas = hangmanCanvas.value;
  if (!canvas) return;
  
  const ctx = canvas.getContext('2d');
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  ctx.lineWidth = 2;
  ctx.strokeStyle = '#333';

  // Dibujo la horca
  ctx.beginPath();
  ctx.moveTo(10, 240); // Base
  ctx.lineTo(100, 240);
  ctx.moveTo(50, 240); // Poste vertical
  ctx.lineTo(50, 10);
  ctx.lineTo(150, 10); // Viga horizontal
  ctx.lineTo(150, 30); // Cuerda
  ctx.stroke();

  drawCharacterParts(ctx);
};

const drawCharacterParts = (ctx) => {
  // Cabeza
  if (props.errors >= 1) {
    ctx.beginPath();
    ctx.arc(150, 60, 30, 0, Math.PI * 2, true);
    ctx.stroke();
  }
  // Cuerpo
  if (props.errors >= 2) {
    ctx.beginPath();
    ctx.moveTo(150, 90);
    ctx.lineTo(150, 170);
    ctx.stroke();
  }
  // Brazo izquierdo
  if (props.errors >= 3) {
    ctx.beginPath();
    ctx.moveTo(150, 100);
    ctx.lineTo(120, 140);
    ctx.stroke();
  }
  // Brazo derecho
  if (props.errors >= 4) {
    ctx.beginPath();
    ctx.moveTo(150, 100);
    ctx.lineTo(180, 140);
    ctx.stroke();
  }
  // Pierna izquierda
  if (props.errors >= 5) {
    ctx.beginPath();
    ctx.moveTo(150, 170);
    ctx.lineTo(120, 210);
    ctx.stroke();
  }
  // Pierna derecha
  if (props.errors >= 6) {
    ctx.beginPath();
    ctx.moveTo(150, 170);
    ctx.lineTo(180, 210);
    ctx.stroke();
  }
  if (props.errors >= 7) {
    ctx.beginPath();
    ctx.arc(140, 55, 3, 0, Math.PI * 2, true);
    ctx.fill();
    ctx.arc(160, 55, 3, 0, Math.PI * 2, true);
    ctx.fill();
  }
  if (props.errors >= 8) {
    ctx.beginPath();
    ctx.arc(150, 70, 10, 0, Math.PI, false);
    ctx.stroke();
  }
};


onMounted(() => {
  drawHangman();
});


watch(() => props.errors, drawHangman);
watch(() => props.character, drawHangman);
</script>

<style scoped>
.hangman-container {
  width: 200px;
  height: 250px;
  border: 1px solid #ddd;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: center;
  align-items: center;
}

canvas {
  display: block;
}
</style>
