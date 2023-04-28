<template>
    <div class="timer">Timer : {{ timeRemaining }}</div>
</template>
  
<script>
  export default {
    props: {
      timeRemaining: Number
    }
  }
</script>
  
<style scoped>
  .timer {
    font-size: 24px;
  }
 </style>