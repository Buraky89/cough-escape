<template>
  <div>
    <ul style="position: absolute;">
      <Player v-for="p in players" :key="p.id" :time="time" :id="p.id" v-on:cough="onCough" :coughList="coughList" @playerDied="onPlayerDied"></Player>
      <Player :id="-1" v-on:cough="onCough" :key="-1" :time="time" :forcedX="forcedX" :forcedY="forcedY" :coughList="coughList" @playerDied="onPlayerDied"></Player>
    </ul>
  </div>
</template>

<script>
import Player from './Player.vue';

export default {
  name: 'MainPlatform',
  props: ['players', 'forcedX', 'forcedY', 'time'],
  data: function(){
    return {
      coughList: []
    }
  },
  methods: {
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
