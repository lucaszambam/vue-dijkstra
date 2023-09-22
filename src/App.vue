<template>
  <Map />
  <div class="github-repo">
    <a href="https://github.com/lucaszambam/vue-dijkstra" target="_blank">
      <img src="./assets/svg/github-mark-white.svg" alt="GitHub Repository" />
    </a>
  </div>
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
.github-repo {
	position: fixed;
    bottom: 10px;
    right: 20px;
}
.github-repo a img {
	scale: 0.45;
    opacity: 0.5;
	transition: all .25s;
}
.github-repo a img:hover {
	opacity: 1;
	scale: 0.5;
}
</style>
