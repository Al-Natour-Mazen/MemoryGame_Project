<template>
    <div class="board">
      <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
        <div class="card" v-for="(card, colIndex) in row" :key="colIndex" 
          @click="flipCard(rowIndex, colIndex)">
            <div class="card-back card-hidden"></div>
            <div class="card-front">{{ card.flipped ? card.value : '' }}</div>
        </div>
      </div>
    </div>
</template>
  
<script>
  export default {
    props: {
      board: Array,
      flippedCards: Array
    },
    methods: {
      flipCard(rowIndex, colIndex) {
        // flip the card and check for a match
        this.$emit('flip-card', rowIndex, colIndex);
      }
    }
  }
</script>
  
<style scoped>
  .board {
    display: flex;
    flex-wrap: wrap;
  }
  
  .row {
    display: flex;
    width: 100%;
    justify-content: space-around;
  }
  
  .card {
    width: 100px;
    height: 100px;
    margin: 10px;
    position: relative;
    perspective: 1000px;
    cursor: pointer;
  }
  .card-hidden {
  display: none;
}
.card-front, .card-back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
}
  
  .card-front {
    background-color: #c9c9c9;
    color: #000;
  }
  
  .card-back {
    background-color: #000;
  }
</style>
  