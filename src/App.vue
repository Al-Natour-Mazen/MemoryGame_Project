<template>
  <div>
    <scoreboard :score="score" :high-score="highScore" />
    <timer :time-remaining="timeRemaining" />
    <board :board="board" :flipped-cards="flippedCards" @flip-card="flipCard" />
    <gameover v-if="gameOver" :message="gameOverMessage" @restart-game="restartGame" />
    <modal v-if="showModal" :title="modalTitle" :message="modalMessage" @close="closeModal" />
  </div>
</template>

<script>
import Scoreboard from './components/ScoreboardView.vue';
import Timer from './components/TimerView.vue';
import Board from './components/BoardView.vue';
import gameover from './components/GameOverview.vue';
import Modal from './components/ModalView.vue';

export default {
  name: 'App',
  components: {
    Scoreboard,
    Timer,
    Board,
    gameover,
    Modal
  },
  data() {
    return {
      score: 0,
      highScore: 0,
      timeRemaining: 60,
      board: [], // array of arrays containing card objects
      flippedCards: [], // array of card objects that are currently flipped
      gameOver: false,
      gameOverMessage: '',
      showModal: false,
      modalTitle: '',
      modalMessage: ''
    };
  },
  mounted() {
    // initialize the game
    this.initializeGame();
  },
  methods: {
    initializeGame() {
      // set up the board
      const cardValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H','I','G'];
      const cards = [];
      cardValues.forEach((value) => {
        cards.push({ value, flipped: false, matched: false });
        cards.push({ value, flipped: false, matched: false });
      });
      const shuffledCards = this.shuffleArray(cards);
      const board = [];
      for (let i = 0; i < 4; i++) {
        board.push(shuffledCards.slice(i * 4, (i + 1) * 4));
      }
      this.board = board;

      // reset the game state
      this.score = 0;
      this.timeRemaining = 60;
      this.flippedCards = [];
      this.gameOver = false;

      // start the timer
      this.timerInterval = setInterval(() => {
        if (this.timeRemaining > 0) {
          this.timeRemaining--;
        } else {
          clearInterval(this.timerInterval);
          this.gameOver = true;
          this.gameOverMessage = 'Time is up!';
          if (this.score > this.highScore) {
            this.highScore = this.score;
            this.showModal = true;
            this.modalTitle = 'New High Score!';
            this.modalMessage = `Congratulations, your new high score is ${this.highScore}!`;
          }
        }
      }, 1000);
    },
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    flipCard(rowIndex, colIndex) {
      // flip the card
      const card = this.board[rowIndex][colIndex];
      card.flipped = true;
      this.flippedCards.push(card);

      // check for a match
      if (this.flippedCards.length === 2) {
        const [card1, card2] = this.flippedCards;
        if (card1.value === card2.value) {
          // cards match
          card1.matched = true;
          card2.matched = true;
          this.score += 2;
          this.flippedCards = [];
        } else {
          // cards don't match
          setTimeout(() => {
          card1.flipped = false;
          card2.flipped = false;
          this.flippedCards = [];
          }, 1000);
        this.score--;
        }
      }
    // check for game over
      if (this.board.every(row => row.every(card => card.matched))) {
        clearInterval(this.timerInterval);
        this.gameOver = true;
        this.gameOverMessage = 'You won!';
        if (this.score > this.highScore) {
          this.highScore = this.score;
          this.showModal = true;
          this.modalTitle = 'New High Score!';
          this.modalMessage = `Congratulations, your new high score is ${this.highScore}!`;
        }
      }
    },
    restartGame() {
      this.initializeGame();
    },
    closeModal() {
      this.showModal = false;
      }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
