<template>
    <div class="board">
      <div v-if="game_Over" >
        <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
            <div class="card" v-for="(card, colIndex) in row" :key="colIndex" >
              <div :class="['card-shape', { 'card-not-flipped': !card.flipped, 'card-front': card.flipped }]">
                {{ card.value }}
              </div>
            </div>
        </div>
      </div>
      <div v-else-if="!game_Over">
        <div class="row" v-for="(row, rowIndex) in board" :key="rowIndex">
            <div class="card" v-for="(card, colIndex) in row" :key="colIndex" 
             @click="flipCard(rowIndex, colIndex)">
                <div :class="['card-shape',{ 'card-front': card.flipped, 'card-back': !card.flipped }]">
                    {{ card.flipped ? card.value : '' }}
                </div>
            </div>    
        </div>
      </div>
    </div>
</template>
  
<script>
  export default {
    props: {
      board: Array,
      flippedCards: Array,
      game_Over: Boolean
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
    justify-content: center;
  }
  
  .row {
    display: flex;
    width: 100%;
    justify-content: space-around;
  }
  
  .card {
    width: 100px;
    height: 100px;
    margin-left: 50px;
    margin-right: 50px;
    margin-top: 10px;
    position: relative;
    perspective: 1000px;
    cursor: pointer;
  }

.card-shape {
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
    background-color: #5c5885;
  }
  .card-not-flipped {
    background-color: #c41e39; /* Choisissez une couleur différente */
    color: aliceblue;
  }
</style>
  