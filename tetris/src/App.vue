<template>
  <NavBar/>
  <Tetris/>
</template>

<script>
import Tetris from './components/Tetris.vue'
import NavBar from './components/NavBar.vue'

export default {
  name: 'App',
  components: {
    Tetris,
    NavBar
  }
}
</script>

<style>
body, html {
  height: 100%;
  width: 100%;
  margin: 0px;
  overflow: hidden;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: #1e1e1e;
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
</style>
