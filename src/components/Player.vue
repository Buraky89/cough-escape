<template>
  <li :key="player.id" :style="getPlayerStyle">{{ player.name }} - {{ player.x }} - {{ player.y }}</li>
</template>

<script>
export default {
  name: 'Player',
  props: ['player'],
  computed: {
    getPlayerStyle() {
      return "position: absolute; top: " + this.player.x.toString() + "px; left: " + this.player.y.toString() + "px;";
    }
  }
}
</script>

<style scoped>

</style>
