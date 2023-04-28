<template>
    <div class="gameover">
      <div class="message">{{ message }}</div>
      <button class="restart" @click="restartGame">Play Again</button>
    </div>
</template>
  
<script>
  export default {
    props: {
      message: String
    },
    methods: {
      restartGame() {
        this.$emit('restart-game');
      }
    }
  }
</script>
  
<style scoped>
  .gameover {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 24px;
  }
  
  .restart {
    margin-top: 20px;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
  }
  </style>