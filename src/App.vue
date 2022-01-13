<template>
  <div id="app">
    <div class="game-container">
      <GameLost v-if="isGameLost" />
      <MainPlatform :players="players" :time="time" v-on:gameLost="onGameLost" v-on:playerDied="onPlayerDied" />
    </div>
    <div class="game-console">
      <button @click="startGame" v-if="isGameLost == false && isGameStarted == false">Start</button>
      <button @click="restartGame" v-if="isGameLost">Restart</button>
      <div class="info">Time: {{ time}}</div>
      <div class="info">Deaths: {{ deathCount }}</div>
    </div>
  </div>
</template>

<script>
import MainPlatform from './components/MainPlatform.vue'
import GameLost from './components/GameLost.vue'

export default {
  name: 'App',
  data: function() {
    return {
      max_id: 0,
      time: 0,
      players: [],
      deathCount: 0,
      isGameStarted: false,
      isGameLost: false,
      tickTimeInterval: null
    };
  },
  components: {
    MainPlatform, GameLost
  },
  methods: {
    onGameLost(){
      this.isGameLost = true;
      clearInterval(this.tickTimeInterval);
    },
    onPlayerDied(id) {
      console.log(id, " died...");
      this.deathCount++;
    },
    startGame(){
      this.tickTimeInterval = setInterval(() => this.tickTime(), 10);
      this.isGameStarted = true;
    },
    restartGame() {
      this.time = 0;
      this.players = [];
      this.deathCount = 0;
      this.isGameLost = false;
      this.max_id = 0;

      this.startGame();
    },
    tickTime(){
      this.time++;

      if(this.time % 1000 == 1){
        this.addRandomPlayer();
      }
    },
    createRandomPlayer(){
      return {"id": ++this.max_id};
    },
    addRandomPlayer(){
      var randomPlayer = this.createRandomPlayer();
      this.players.push(randomPlayer);
    }
  }
}

</script>

<style scoped>
  .game-container {display: block; height: 500px;}
  .game-console {display: block; width: 480px; height: 35px; background-color: purple; border: 1px solid purple; padding: 10px;}
  .info {display: inline-block; float: right; border: 0; border-radius: 10px; background-color: #fff; color: purple; background: ccc; font-family: Verdana; padding: 10px; width: 150px; overflow: hidden; text-align: center; margin: 0 0 0 5px;}
  button {display: inline-block; padding: 10px; border-radius: 10px; color: purple; background-color: #fff; border: 0;}
</style>
