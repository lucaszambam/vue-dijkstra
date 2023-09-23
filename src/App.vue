<template>
  <HeaderPanel />
  <div class="map-container">
    <Map />
  </div>
  <div class="github-repo">
    <a href="https://github.com/lucaszambam/vue-dijkstra" target="_blank">
      <img src="./assets/svg/github-mark-white.svg" alt="GitHub Repository" />
    </a>
  </div>
</template>

<script>
import HeaderPanel from './components/HeaderPanel.vue';
import Map from './components/Map.vue'

export default {
  name: 'App',
  components: {
    Map,
    HeaderPanel
}
}
</script>

<style>
#app {
  height: 100vh;
  display: flex;
  align-items: center;
  flex-direction: column;
  font-family: 'Roboto Mono';
}
.map-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}
body {
  background-color: #0d1117;
  min-height: 100vh;
  margin: 0;
  width: 100vw;
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
