<template>
  <div id="app">
    <div class="container">
      <h1 class="simon__title">Simon Game</h1>
      <div class="simon__content">
        <div class="simon__square">
          <button
            :disabled="!clickable"
            @click="clickPart(1)"
            ref="1"
            class="simon__part blue"
          ></button>
          <button
            :disabled="!clickable"
            @click="clickPart(2)"
            ref="2"
            class="simon__part red"
          ></button>
          <button
            :disabled="!clickable"
            @click="clickPart(3)"
            ref="3"
            class="simon__part yellow"
          ></button>
          <button
            :disabled="!clickable"
            @click="clickPart(4)"
            ref="4"
            class="simon__part green"
          ></button>
        </div>
        <div class="simon__settings">
          <h2 class="simon__settings-title">Round: {{ round.length }}</h2>
          <button :disabled="gameStarted" @click="start" class="simon__button">
            Start
          </button>
          <p v-if="lose" class="simon__lose">Sorry, you lose.</p>
          <div class="simon__options">
            <p class="simon__options-title">Game Options:</p>
            <input
              v-model="level"
              name="mode"
              type="radio"
              class="simon__radio"
              value="easy"
            />
            <span class="simon__level">Easy</span>
            <br />
            <input
              v-model="level"
              name="mode"
              type="radio"
              class="simon__radio"
              value="normal"
            />
            <span class="simon__level">Normal</span>
            <br />
            <input
              v-model="level"
              name="mode"
              type="radio"
              class="simon__radio"
              value="hard"
            />
            <span class="simon__level">Hard</span>
            <br />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import sound1 from "@/assets/sound/1.mp3";
import sound2 from "@/assets/sound/2.mp3";
import sound3 from "@/assets/sound/3.mp3";
import sound4 from "@/assets/sound/4.mp3";

export default {
  data() {
    return {
      gameStarted: false,
      round: [],
      level: "easy",
      currentRound: [],
      lose: false,
      clickable: true,
    };
  },
  computed: {
    levelNumber() {
      return this.level == "easy" ? 1500 : this.level == "normal" ? 1000 : 400;
    },
  },
  methods: {
    start() {
      this.gameStarted = true;
      this.lose = false;
      this.addRound();
    },
    addRound() {
      this.round.push(this.getRandom());
      this.playRound();
    },
    async playRound() {
      await this.sleep(200);
      this.clickable = false;
      for (let i = 1; i < this.round.length + 1; i++) {
        this.soundPlay(this.round[i - 1]);
        this.$refs[this.round[i - 1]].classList.add("active");
        await this.sleep(this.levelNumber);
        this.$refs[this.round[i - 1]].classList.remove("active");
        await this.sleep(200);
      }
      this.clickable = true;
    },

    sleep(m) {
      return new Promise((r) => setTimeout(r, m));
    },

    gameLose() {
      this.round = [];
      this.currentRound = [];
      this.lose = true;
      this.gameStarted = false;
    },

    clickPart(part) {
      this.soundPlay(part);
      this.currentRound.push(part);
      let arr = this.round.slice(0, this.currentRound.length);
      if (JSON.stringify(arr) === JSON.stringify(this.currentRound)) {
        if (this.currentRound.length == this.round.length) {
          this.currentRound = [];
          this.addRound();
        }
      } else {
        this.gameLose();
      }
    },

    soundPlay(num) {
      if (num == 1) {
        new Audio(sound1).play();
      } else if (num == 2) {
        new Audio(sound2).play();
      } else if (num == 3) {
        new Audio(sound3).play();
      } else {
        new Audio(sound4).play();
      }
    },

    getRandom() {
      return Math.floor(Math.random() * 4) + 1;
    },
  },
};
</script>

<style lang="sass">
body
  font-family: 'Roboto', sans-serif !important
*
  box-sizing: border-box
.container
  width: 100%
  max-width: 540px
  margin: 0 auto
  margin-top: 10%
.simon
  &__title
    text-align: center
    font-size: 38px
    margin-bottom: 50px
  &__content
    display: flex
  &__button
    cursor: pointer
    border: none
    background-color: #4189cc
    font-size: 20px
    padding: 10px 50px
    border-radius: 20px
    margin-bottom: 10px
  &__lose
    margin-bottom: 10px
  &__options
    &-title
      font-size: 20px
      margin-bottom: 10px
  &__level
    display: inline-block
    font-size: 16px
    margin-bottom: 12px
  &__settings
    &-title
      margin-bottom: 30px
      font-size: 30px
  &__square
    margin-right: 80px
    display: grid
    grid-template-columns: repeat(2, 1fr)
    grid-template-rows: repeat(2,1fr)
  &__part
    width: 150px
    height: 150px
    opacity: 0.6
    border: none
    cursor: pointer
    &:active
      opacity: 1
    &:disabled
      &:active
        opacity: 0.6
    &.red
      background-color: #f5220f
      border-top-right-radius: 150px
      &:hover
        border-top: 3px solid #4a0600
        border-right: 3px solid #4a0600
    &.green
      background-color: #3ccf63
      border-end-end-radius: 150px
      &:hover
        border-bottom: 3px solid #4a0600
        border-right: 3px solid #4a0600
    &.yellow
      background-color: #d0ff00
      border-end-start-radius: 150px
      &:hover
        border-bottom: 3px solid #4a0600
        border-left: 3px solid #4a0600
    &.blue
      background-color: #4189cc
      border-top-left-radius: 150px
      &:hover
        border-top: 3px solid #4a0600
        border-left: 3px solid #4a0600
.active
  opacity: 1
</style>
