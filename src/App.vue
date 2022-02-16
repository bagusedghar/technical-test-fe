<template>
  <div id="app">
    <Books/>
  </div>
</template>

<script>
import Books from './components/Books.vue'

export default {
  name: 'App',
  components: {
    Books
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #000000;
  background-color:antiquewhite;
  padding-top: 60px;
  min-height: 100vh;
}
</style>
