<template>
  <div id="app">
    <IsometricMap/>
  </div>
</template>

<script>
import IsometricMap from './components/IsometricMap'

export default {
  name: 'App',
  components: {
    IsometricMap
  }
}
</script>

<style>
body {
  padding: 0;
  margin: 0;
}
</style>
