<template>
  <div style="border: 1px solid #ccc; width: 500px; height: 500px; position: absolute;" @click="onMouseClick">
    <Player v-for="p in players" :key="p.id" :time="time" :id="p.id" v-on:cough="onCough" :coughList="coughList" @playerDied="onPlayerDied"></Player>
    <Player :id="-1" v-on:cough="onCough" :key="-1" :time="time" :forcedX="forcedTarget.x" :forcedY="forcedTarget.y" :coughList="coughList" @playerDied="onPlayerDied"></Player>
  </div>
</template>

<script>
import Player from './Player.vue';

export default {
  name: 'MainPlatform',
  props: ['players', 'time'],
  data: function(){
    return {
      coughList: [],
      playerRadius: 15,
      forcedTarget: {
        x: null,
        y: null
      }
    }
  },
  methods: {
    onMouseClick(e){
      if(e.target.className != "player"){ // TODO: find a better way to refrain from clicks on other players
        this.forcedTarget = this.getNormalizedLocation(e.offsetX, e.offsetY);
      }
    },
    onCough(details){
      this.$emit('cough', details);

      this.coughList.push(details);
    },
    onPlayerDied(id){
      this.$emit('playerDied', id);
      if(id == -1){
        this.$emit('gameLost');
      }
    },
    getNormalizedLocation(x, y){
      var newLocation = {"x": x - this.playerRadius, "y": y - this.playerRadius};
      if(newLocation.x > 500 - this.playerRadius * 2) newLocation.x = 500 - this.playerRadius * 2;
      if(newLocation.y > 500 - this.playerRadius * 2) newLocation.y = 500 - this.playerRadius * 2;
      // TODO: allow 0 to be used as well
      if(newLocation.x <= 0) newLocation.x = 1;
      if(newLocation.y <= 0) newLocation.y = 1;
      return newLocation;
    }
  },
  components: {
    Player
  }
}
</script>

<style scoped>

</style>
