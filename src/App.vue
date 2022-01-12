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
      var id = ++this.max_id;

      if(id == 1){ // TODO: find a better solution to initiate interval
        setInterval(() => this.tickTime(), 50)
      }

      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      var nextX = this.getRandomInt(500);
      var nextY = this.getRandomInt(500);

      return {"id": id, "name": randomName, "x": x, "y": y, "nextX": nextX, "nextY": nextY};
    },
    moveSinglePlayer(index){
      var singlePlayer = this.players[index];
      
      var yMovementDirection = Math.sign(singlePlayer.nextY - singlePlayer.y);
      var xMovementDirection = Math.sign(singlePlayer.nextX - singlePlayer.x);
      var speed = 1;

      var yDifference = speed * yMovementDirection;
      var xDifference = speed * xMovementDirection * Math.abs(((singlePlayer.nextY - singlePlayer.y) / (singlePlayer.nextX - singlePlayer.x)));

      singlePlayer.x = singlePlayer.x + xDifference;
      singlePlayer.y = singlePlayer.y + yDifference;

      var playerIsCloseEnoughToTarget = false;
      if(Math.abs(singlePlayer.x - singlePlayer.nextX) < 5){
        playerIsCloseEnoughToTarget = true;
      }
      if(Math.abs(singlePlayer.y - singlePlayer.nextY) < 5){
        playerIsCloseEnoughToTarget = true;
      }

      if(playerIsCloseEnoughToTarget){
        
        var nextX = this.getRandomInt(500);
        var nextY = this.getRandomInt(500);

        singlePlayer.nextX = nextX;
        singlePlayer.nextY = nextY;
      }
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
