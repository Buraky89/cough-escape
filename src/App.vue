<template>
  <div id="app">
    <div style="border: 1px solid #ccc; width: 500px; height: 500px;" @click="onMouseClick">
      <GameLost v-if="isGameLost" />
      <MainPlatform :players="players" :time="time" :forcedX="forcedTarget.x" :forcedY="forcedTarget.y" v-on:cough="onCough" v-on:gameLost="onGameLost" />
    </div>

    <button @click="startGame" v-if="isGameLost == false && isGameStarted == false">Start</button>
    <button @click="restartGame" v-if="isGameLost">Restart</button>
    
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
      isGameStarted: false,
      isGameLost: false,
      forcedTarget: {
        x: null,
        y: null
      },
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
    onCough(details){
      console.log("cough got from player. ", details.id, details.x, details.y, details.period);
    },
    onMouseClick(e){
      this.forcedTarget = {"x": e.offsetX, "y": e.offsetY};
    },
    startGame(){
      this.tickTimeInterval = setInterval(() => this.tickTime(), 10);
      this.isGameStarted = true;
    },
    restartGame() {
      this.time = 0;
      this.players = [];
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

<style>

</style>
