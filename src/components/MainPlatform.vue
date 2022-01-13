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
      forcedTarget: {
        x: null,
        y: null
      }
    }
  },
  methods: {
    onMouseClick(e){
      this.forcedTarget = {"x": e.offsetX, "y": e.offsetY};
    },
    onCough(details){
      this.$emit('cough', details);

      this.coughList.push(details);
    },
    onPlayerDied(id){
      console.log("player died: ", id);
      if(id == -1){
        this.$emit('gameLost');
      }
    }
  },
  components: {
    Player
  }
}
</script>

<style scoped>

</style>
