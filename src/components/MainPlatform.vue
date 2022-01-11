<template>
  <div>
    <ul>
      <li v-for="p in players" :key="p.id">{{ p.id }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'MainPlatform',
  props: ['players']
}
</script>

<style scoped>

</style>
