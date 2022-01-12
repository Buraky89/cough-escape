<template>
  <div id="app">
    <button @click="addRandomPlayer">Create</button>
    
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
        setInterval(() => this.tickTime(), 10)
      }

      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      var nextLocation = this.decideNextLocation(x, y);
      var nextX = nextLocation.x;
      var nextY = nextLocation.y;

      return {"id": id, "name": randomName, "x": x, "y": y, "nextX": nextX, "nextY": nextY};
    },
    moveSinglePlayer(index){
      var singlePlayer = this.players[index];
      
      var yMovementDirection = Math.sign(singlePlayer.nextY - singlePlayer.y);
      var xMovementDirection = Math.sign(singlePlayer.nextX - singlePlayer.x);
      var speed = 1;

      var alpha = Math.atan(Math.abs((singlePlayer.nextX - singlePlayer.x) / (singlePlayer.nextY - singlePlayer.y)));
      var yDifference = speed * yMovementDirection * Math.cos(alpha);
      var xDifference = speed * xMovementDirection * Math.sin(alpha);

      singlePlayer.x = singlePlayer.x + xDifference;
      singlePlayer.y = singlePlayer.y + yDifference;

      var howClose = Math.sqrt(Math.pow(singlePlayer.x - singlePlayer.nextX, 2) + Math.pow(singlePlayer.y - singlePlayer.nextY, 2));
      var playerIsCloseEnoughToTarget = howClose < 4;

      if(playerIsCloseEnoughToTarget){
        var nextLocation = this.decideNextLocation(singlePlayer.x, singlePlayer.y);
        var nextX = nextLocation.x;
        var nextY = nextLocation.y;

        singlePlayer.nextX = nextX;
        singlePlayer.nextY = nextY;
      }
    },
    decideNextLocation(x, y){
      var newPoint = this.generateRandomPoint(x, y, 100);
      while(newPoint.x < 0 || newPoint.x >= 500 ||newPoint.y < 0 || newPoint.y >= 500){
        console.log("randoming again...");
        newPoint = this.generateRandomPoint(x, y, 100);
      }
      return {x: newPoint.x, y: newPoint.y};
    },
    generateRandomPoint(x, y, distance) {
        var alpha = this.getRandomInt(360);
        return {"x": (x + distance * Math.cos(alpha)), "y": (y + distance * Math.sin(alpha))}
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
