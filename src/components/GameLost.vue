<template>
  <div class="game-lost">You have lost!</div>
</template>

<script>
export default {
  name: 'GameLost'
}
</script>

<style scoped>
.game-lost {
  width: 500px;
  height: 500px;
  display: inline-block;
  background: yellow;
  position: absolute;
  z-index: 99;
  font-size: 22px;
  font-family: Verdana;
  text-align: center;
  line-height: 500px;
}

</style>
