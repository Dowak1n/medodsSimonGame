<template>
  <main class="game">
    <section class="square-container" :class="{ 'unclickable': !isClickable }">
      <div class="square square-red" data-square="red" ref="red" @click="handleClick('red')"></div>
      <div class="square square-green" data-square="green" ref="green" @click="handleClick('green')"></div>
      <div class="square square-blue" data-square="blue" ref="blue" @click="handleClick('blue')"></div>
      <div class="square square-yellow" data-square="yellow" ref="yellow" @click="handleClick('yellow')"></div>
    </section>

    <footer>
      <div>
        <button class="difficulty-button" @click="setDifficulty('easy')">Easy</button>
        <button class="difficulty-button" @click="setDifficulty('normal')">Normal</button>
        <button class="difficulty-button" @click="setDifficulty('hard')">Hard</button>
      </div>
      <button class="start-button" @click="startGame">Start</button>
    </footer>

    <div class="hidden">
      <audio
        ref="redAudio"
        src="../assets/red.mp3"
        data-sound="red"
      ></audio>
      <audio
        ref="greenAudio"
        src="../assets/green.mp3"
        data-sound="green"
      ></audio>
      <audio
        ref="blueAudio"
        src="../assets/blue.mp3"
        data-sound="blue"
      ></audio>
      <audio
        ref="yellowAudio"
        src="../assets/yellow.mp3"
        data-sound="yellow"
      ></audio>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      sequence: [],
      humanSequence: [],
      level: 0,
      colors: ['red', 'green', 'blue', 'yellow'],
      gameStarted: false,
      difficulty: 'normal'
    };
  },
  computed: {
    isClickable() {
      return this.gameStarted && this.humanSequence.length !== this.sequence.length;
    },
    delay() {
      switch (this.difficulty) {
        case 'easy':
          return 1500;
        case 'normal':
          return 1000;
        case 'hard':
          return 400;
        default:
          return 1000;
      }
    }
  },
  methods: {
    resetGame(text) {
      alert(text);
      this.sequence = [];
      this.humanSequence = [];
      this.level = 0;
      this.gameStarted = false;
    },
    userTurn() {
  this.$nextTick(() => {
    Object.values(this.$refs).forEach(square => {
      square.classList.remove('activated');
    });
  });
},
    activateSquare(color) {
      const square = this.$refs[color];
      const audio = this.$refs[color + 'Audio'];

      square.classList.add('activated');
      audio.play();

      setTimeout(() => {
        square.classList.remove('activated');
      }, 300);
    },
    playRound(nextSequence) {
      nextSequence.forEach((color, index) => {
        setTimeout(() => {
          this.activateSquare(color);
        }, (index + 1) * this.delay);
      });
    },
    nextStep() {
      const random = this.colors[Math.floor(Math.random() * this.colors.length)];
      return random;
    },
    nextRound() {
      this.level += 1;
      this.gameStarted = false;

      const nextSequence = [...this.sequence];
      nextSequence.push(this.nextStep());
      this.playRound(nextSequence);

      this.sequence = [...nextSequence];
      setTimeout(() => {
        this.userTurn();
        this.gameStarted = true;
      }, this.level * this.delay + 1000);
    },
    handleClick(color) {
      if (!this.gameStarted || !this.isClickable) return;

      const index = this.humanSequence.push(color) - 1;
      const audio = this.$refs[color + 'Audio'];

      audio.play();

      if (this.humanSequence[index] !== this.sequence[index]) {
        this.resetGame('You lose!');
        return;
      }

      if (this.humanSequence.length === this.sequence.length) {
        this.humanSequence = [];
        setTimeout(() => {
          this.nextRound();
        }, 1000);
        return;
      }
    },
    startGame() {
      this.gameStarted = true;
      this.nextRound();
    },
    setDifficulty(difficulty) {
      this.difficulty = difficulty;
    }
  }
};
</script>

<style>
.hidden {
  display: none !important;
}
.invisble {
  visibility: hidden;
}
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  display: flex;
  justify-content: center;
}
.game {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.square-container {
  display: flex;
}
.unclickable {
  pointer-events: none;
}
.square {
  cursor: pointer;
  height: 100px;
  width: 100px;
}
.square-red {
  background-color: #a8323e;
}
.square-red:hover,
.square-red:focus {
  background-color: #d16d77;
}
.square-red:active,
.square-red.activated {
  background-color: #d16d77;
}
.square-green {
  background-color: #3cab30;
}
.square-green:hover,
.square-green:focus {
  background-color: #72c969;
}
.square-green:active,
.square-green.activated {
  background-color: #72c969;
}
.square-yellow {
  background-color: #f5bb33;
}
.square-yellow:hover,
.square-yellow:focus {
  background-color: #f0c359;
}
.square-yellow:active,
.square-yellow.activated {
  background-color: #f0c359;
}
.square-blue {
  background-color: #408eed;
}
.square-blue:hover,
.square-blue:focus {
  background-color: #6aa4eb;
}
.square-blue:active,
.square-blue.activated {
  background-color: #6aa4eb;
}
.start-button {
  font-size: 24px;
  padding: 12px;
  cursor: pointer;
}
</style>