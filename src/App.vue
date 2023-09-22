<template>
  <Map />
</template>

<script>
import Map from './components/Map.vue'

export default {
  name: 'App',
  components: {
    Map
  }
}
</script>

<style>
#app {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
body {
  background-color: #0d1117;
  height: 99vh;
}
</style>
