<template>

</template>

<script>
import * as TRAVISO from 'traviso.js';
export default {
  name: 'Traviso',
  data: () => ({
    pixiRoot: new PIXI.Application({
      width: 800,
      height: 600,
      backgroundColor: 0xffffff,
    })
  })
}
</script>

<style scoped>

</style>
