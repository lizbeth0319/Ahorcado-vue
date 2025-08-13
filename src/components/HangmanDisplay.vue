 <template>
  <div class="hangman-container">
    <canvas ref="hangmanCanvas" width="200" height="250"></canvas>
  </div>
</template>

<script>
export default {
  name: 'HangmanDisplay',
  props: ['errors', 'character'],
  watch: {
    errors: 'drawHangman',
    character: 'drawHangman',
  },
  mounted() {
    this.drawHangman();
  },
  methods: {
    drawHangman() {
      const canvas = this.$refs.hangmanCanvas;
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      ctx.lineWidth = 2;
      ctx.strokeStyle = '#333';

      // Draw gallows (static)
      ctx.beginPath();
      ctx.moveTo(10, 240); // Base
      ctx.lineTo(100, 240);
      ctx.moveTo(50, 240); // Vertical post
      ctx.lineTo(50, 10);
      ctx.lineTo(150, 10); // Horizontal beam
      ctx.lineTo(150, 30); // Rope
      ctx.stroke();

      // Draw character parts based on errors
      this.drawCharacterParts(ctx);
    },
    drawCharacterParts(ctx) {
      // Head
      if (this.errors >= 1) {
        ctx.beginPath();
        ctx.arc(150, 60, 30, 0, Math.PI * 2, true);
        ctx.stroke();
      }
      // Body
      if (this.errors >= 2) {
        ctx.beginPath();
        ctx.moveTo(150, 90);
        ctx.lineTo(150, 170);
        ctx.stroke();
      }
      // Left Arm
      if (this.errors >= 3) {
        ctx.beginPath();
        ctx.moveTo(150, 100);
        ctx.lineTo(120, 140);
        ctx.stroke();
      }
      // Right Arm
      if (this.errors >= 4) {
        ctx.beginPath();
        ctx.moveTo(150, 100);
        ctx.lineTo(180, 140);
        ctx.stroke();
      }
      // Left Leg
      if (this.errors >= 5) {
        ctx.beginPath();
        ctx.moveTo(150, 170);
        ctx.lineTo(120, 210);
        ctx.stroke();
      }
      // Right Leg
      if (this.errors >= 6) {
        ctx.beginPath();
        ctx.moveTo(150, 170);
        ctx.lineTo(180, 210);
        ctx.stroke();
      }
      // Eyes (example for more errors, up to 8 as per easy difficulty)
      if (this.errors >= 7) {
        ctx.beginPath();
        ctx.arc(140, 55, 3, 0, Math.PI * 2, true);
        ctx.fill();
        ctx.arc(160, 55, 3, 0, Math.PI * 2, true);
        ctx.fill();
      }
      // Mouth (sad)
      if (this.errors >= 8) {
        ctx.beginPath();
        ctx.arc(150, 70, 10, 0, Math.PI, false);
        ctx.stroke();
      }
    },
  },
};
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