<template>
  <div id="app">
    <MainPlatform :players="players"/>
  </div>
</template>

<script>
import MainPlatform from './components/MainPlatform.vue'

export default {
  name: 'App',
  data: function() {
    return {
      players: [{"id": 0}, {"id": 1}, {"id": 2}, {"id": 3}]
    };
  },
  components: {
    MainPlatform
  }
}
</script>

<style>

</style>
