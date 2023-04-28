<template>
    <div class="scoreboard">
      <div class="score">Score: {{ score }}</div>
      <div class="highscore">High Score: {{ highScore }}</div>
    </div>
  </template>
  
<script>
  export default {
    props: {
      score: Number,
      highScore: Number
    }
  }
</script>
  
  <style scoped>
  .scoreboard {
    display: flex;
    justify-content: space-between;
    font-size: 24px;
  }
  
  .score {
    margin-right: 10px;
  }
  
  .highscore {
    margin-left: 10px;
  }
  </style>