<template>
  <div id="app">
    <button @click="startGame">Start</button>
    
    <div style="border: 1px solid #ccc; width: 500px; height: 500px;" @click="onMouseClick">
      <GameLost v-if="isGameLost" />
      <MainPlatform :players="players" :me="me"/>
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
      nearbyPlayers: [],
      isGameLost: false,
      me: {
        name: "You",
        x: null,
        y: null,
        nextX: null,
        nextY: null,
        isMe: true
      }
    };
  },
  components: {
    MainPlatform, GameLost
  },
  methods: {
    onMouseClick(e){
      this.me.nextX = e.offsetX;
      this.me.nextY = e.offsetY;
      console.log(this.me);
    },
    createRandomPlayer() {
      var id = ++this.max_id;

      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      var nextLocation = this.decideNextLocation(x, y);
      var nextX = nextLocation.x;
      var nextY = nextLocation.y;

      return {"id": id, "name": randomName, "x": x, "y": y, "nextX": nextX, "nextY": nextY, "isMe": false};
    },
    startGame(){
      setInterval(() => this.tickTime(), 10);
      this.initiateMe();
    },
    initiateMe(){
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      this.me.x = x;
      this.me.y = y;

      var nextLocation = this.decideNextLocation(x, y);
      var nextX = nextLocation.x;
      var nextY = nextLocation.y;

      this.me.nextX = nextX;
      this.me.nextY = nextY;

      console.log("me initiated");
    },
    moveSinglePlayer(singlePlayer){
      if(this.isGameLost) return;
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

      if(playerIsCloseEnoughToTarget && !singlePlayer.isMe){
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

      if(this.time % 1000 == 1){
        this.addRandomPlayer();
      }
      this.moveSinglePlayer(this.me);
      for(var i = 0; i < this.players.length; i++){
        this.moveSinglePlayer(this.players[i]);
      }
      this.findNearbyPlayers();
    },
    findNearbyPlayers(){
      this.nearbyPlayers = this.players.filter(p => { return Math.sqrt(Math.pow(this.me.x - p.x, 2) + Math.pow(this.me.y - p.y, 2)) < 20 });

      if(this.nearbyPlayers.length > 0){
        this.isGameLost = true;
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
