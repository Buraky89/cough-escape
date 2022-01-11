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

      return {"id": id, "name": randomName};
    },
    addRandomPlayer(){
      var randomPlayer = this.createRandomPlayer();
      this.players.push(randomPlayer);
    }
  }
}
</script>

<style>

</style>
