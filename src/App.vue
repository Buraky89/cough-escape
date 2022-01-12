<template>
  <div id="app">
    <button @click="addRandomPlayer">Create</button>
    <button @click="tickTime">Tick Time</button>
    
    <MainPlatform :players="players"/>
  </div>
</template>

<script>
import MainPlatform from './components/MainPlatform.vue'

export default {
  name: 'App',
  data: function() {
    return {
      max_id: 0,
      time: 0,
      players: []
    };
  },
  components: {
    MainPlatform
  },
  methods: {
    createRandomPlayer() {
      var id = ++this.max_id + 1;
      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      var nextX = this.getRandomInt(500);
      var nextY = this.getRandomInt(500);

      return {"id": id, "name": randomName, "x": x, "y": y, "nextX": nextX, "nextY": nextY};
    },
    moveSinglePlayer(index){
      var singlePlayer = this.players[index];
      singlePlayer.x = singlePlayer.nextX;
      singlePlayer.y = singlePlayer.nextY;
      
      var nextX = this.getRandomInt(500);
      var nextY = this.getRandomInt(500);
      
      singlePlayer.nextX = nextX;
      singlePlayer.nextY = nextY;
    },
    tickTime(){
      this.time++;
      for(var i = 0; i < this.players.length; i++){
        this.moveSinglePlayer(i);
      }
    },
    addRandomPlayer(){
      var randomPlayer = this.createRandomPlayer();
      this.players.push(randomPlayer);
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * max);
    }
  }
}
</script>

<style>

</style>
