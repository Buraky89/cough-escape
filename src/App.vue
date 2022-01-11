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
      players: []
    };
  },
  components: {
    MainPlatform
  },
  methods: {
    createRandomPlayer() {
      var id = ++this.max_id + 1;
      var randomName = this.$faker().name.firstName();
      var x = this.getRandomInt(500);
      var y = this.getRandomInt(500);

      return {"id": id, "name": randomName, "x": x, "y": y};
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
