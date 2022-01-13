<template>
  <div>
    <ul style="position: absolute;">
      <Player v-for="p in players" :key="p.id" :time="time" :id="p.id" v-on:cough="onCough"></Player>
      <Player :id="-1" :key="-1" :time="time" :forcedX="forcedX" :forcedY="forcedY"></Player>
    </ul>
  </div>
</template>

<script>
import Player from './Player.vue';

export default {
  name: 'MainPlatform',
  props: ['players', 'forcedX', 'forcedY', 'time'],
  methods: {
    onCough(details){
      this.$emit('cough', details);
    }
  },
  components: {
    Player
  }
}
</script>

<style scoped>

</style>
