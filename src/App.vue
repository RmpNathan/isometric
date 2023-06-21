<template>
  <v-app>
    <v-main>
      <Scene/>
    </v-main>
  </v-app>
</template>

<script>

import Scene from '@/components/Scene'
export default {
  name: 'App',
  components: { Scene }
};
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
}


</style>
