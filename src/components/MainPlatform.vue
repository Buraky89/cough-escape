<template>
  <div>
    <ul style="position: absolute;">
      <Player v-for="p in players" :key="p.id" :player="p"></Player>
      <Player v-if="me.x" :key="-1" :player="me"></Player>
    </ul>
  </div>
</template>

<script>
import Player from './Player.vue';

export default {
  name: 'MainPlatform',
  props: ['players', 'me'],
  components: {
    Player
  }
}
</script>

<style scoped>

</style>
