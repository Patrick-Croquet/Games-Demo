<template>
  <div id="app">
    <header>
      <h1>Tetris Vue.js</h1>
    </header>
    <main>
      <tetris-grid />
    </main>
  </div>
</template>

<script>
import TetrisGrid from './components/TetrisGrid.vue'

export default {
  name: 'App',
  components: {
    TetrisGrid
  }
}
</script>

<style>
#app {
  font-family: 'Arial', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 20px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

header {
  margin-bottom: 2rem;
}

h1 {
  color: #34495e;
  font-size: 2.5rem;
}
</style>