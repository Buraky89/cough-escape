<template>
  <div>
    <ul style="position: absolute;">
      <Player v-for="p in players" :key="p.id" :player="p"></Player>
    </ul>
  </div>
</template>

<script>
import Player from './Player.vue';

export default {
  name: 'MainPlatform',
  props: ['players'],
  components: {
    Player
  }
}
</script>

<style scoped>

</style>
