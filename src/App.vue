<template>
  <div id="myappbody">
    <scoreboard :score="score" :high-score="highScore" />
    <timer :time-remaining="timeRemaining" />
    <board :board="board" :flipped-cards="flippedCards" @flip-card="flipCard" :game_Over="gameOverBoard" />
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
      timeRemaining: 0,
      numberofcard : 0,
      board: [], // array of arrays containing card objects
      flippedCards: [], // array of card objects that are currently flipped
      gameOver: false,
      gameOverBoard : false,
      gameOverMessage: '',
      showModal: false,
      modalTitle: '',
      modalMessage: '',
    };
  },
  mounted() {
    // initialize the game
    this.initializeGame();
  },
  methods: {
    initializeGame() {
      // init the game state
      this.score = 0;
      this.timeRemaining = 30;
      this.flippedCards = [];
      this.gameOver = false;
      this.gameOverBoard = false;
      
       // ask user for number of cards
       let nb_cartes = parseInt(prompt("Combien de cartes voulez-vous utiliser ?"));
      while (nb_cartes % 2 !== 0) {
        nb_cartes = parseInt(prompt("Le nombre de cartes doit être pair. Combien de cartes voulez-vous utiliser ?"));
      }
      this.numberofcard = nb_cartes;

      
      // set up cars
      // const cardValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'G'];
      const cardValues = [];
      while (cardValues.length < this.numberofcard) {
        const word = this.generateWord(1).toUpperCase(); // generates a X-letter word
        if (cardValues.indexOf(word) === -1) { // check if the card has not already been added
          cardValues.push(word);
          cardValues.push(word);
        }
      }
    
      const cardsShuffled = this.ShuffleCards(cardValues);
      // set up the board
      const board = this.createBoard(cardsShuffled, cardValues.length);
      this.board = board;

      // start the timer
      this.timerInterval = setInterval(() => {
        if (this.timeRemaining > 0) {
          this.timeRemaining--;
        } else {
          this.GamerOvermethode("");
        }
      }, 1000);
    },
    GamerOvermethode(score){
      clearInterval(this.timerInterval);
      this.gameOverBoard = true;
      this.showModal = true;
      if(score === "score"){
        this.modalTitle = "Too much attempt !"
      }else{
        this.modalTitle = 'Time is up !';
      }
     
      this.modalMessage = 'Here is the game solution :';
      setTimeout(() => {
        this.gameOver = true;
        this.gameOverMessage = 'Try again 🎮';
      }, 500);
      this.handleHighScore();
    },
    ShuffleCards(cardValues) {
      const cards = [];
      cardValues.forEach((value) => {
        cards.push({ value, flipped: false, matched: false });
      });
      return this.shuffleArray(cards);
    },
    generateWord(length) {
      const letters = 'abcdefghijklmnopqrstuvwxyz';
      let word = '';
      for (let i = 0; i < length; i++) {
        const randomIndex = Math.floor(Math.random() * letters.length);
        const letter = letters.charAt(randomIndex);
        word += letter;
      }
        return word;
    },
    createBoard(cards, numValues) {
      const board = [];
      const maxCardsPerLine = 5;
      const numRows = Math.ceil(numValues / maxCardsPerLine);
      
      let cardsAdded = 0;
      for (let i = 0; i < numRows; i++) {
        const row = [];
        for (let j = 0; j < maxCardsPerLine; j++) {
          if (cardsAdded < numValues) {
            row.push(cards[cardsAdded]);
            cardsAdded++;
          } else {
            break;
          }
        }
        board.push(row);
      }  
      return board;
    },
    handleHighScore() {
      if (this.score > this.highScore) {
        this.highScore = this.score;
        this.showModal = true;
        this.modalTitle = 'New High Score!';
        this.modalMessage = `Congratulations, your new high score is ${this.highScore}!`;
      }
    },
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    flipCard(rowIndex, colIndex) {
      // Get the card 
      const card = this.board[rowIndex][colIndex];

      if (card.flipped) {
        return;
      }

      // flip the card
      card.flipped = true;
      this.flippedCards.push(card);

      //// check for a match with another flipped card
      if (this.flippedCards.length === 2) {
        const [card1, card2] = this.flippedCards;
        if (card1.value === card2.value) {
          // cards match
          this.handleMatchingCards(card1, card2);
        } else {
          // cards don't match
          this.handleNonMatchingCards(card1, card2);
        }
      }

      // check for game win
      if (this.checkForGameWin()) {
        this.handleGameWin();
      }
    },
    handleMatchingCards(card1, card2) {
      card1.matched = true;
      card2.matched = true;
      this.score += 1;
      this.flippedCards = [];
    },
    handleNonMatchingCards(card1, card2) {
      setTimeout(() => {
        card1.flipped = false;
        card2.flipped = false;
        this.flippedCards = [];
      }, 600);
      this.score--;
      if (this.score < -10) {
        this.gameOver("score");
      }
    },
    checkForGameWin() {
      return this.board.every(row => row.every(card => card.matched));
    },
    handleGameWin() {
      clearInterval(this.timerInterval);
      this.gameOver = true;
      this.gameOverMessage = 'You won!';
      if (this.score > this.highScore) {
        this.highScore = this.score;
        this.showModal = true;
        this.modalTitle = 'New High Score!';
        this.modalMessage = `Congratulations, your new high score is ${this.highScore}!`;
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
#myappbody {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
