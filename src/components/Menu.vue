<template>
  <div class="menu">
    <h1 class="menu-title">Controle</h1>
    <v-expansion-panels>
      <v-expansion-panel
          v-for="(light, index) in allLights" :key="index"
      >
        <v-expansion-panel-header>
          {{ light.name }}
        </v-expansion-panel-header>
        <v-expansion-panel-content>
          <v-color-picker v-model="selectedColors[index]" @input="updateColorForOneLight(index, light)"></v-color-picker>
          <v-text-field v-model="light.intensity" type="number" label="Intensity"/>
          <v-text-field v-model="light.shadow.mapSize.width" type="number" label="Shadow width"/>
          <v-text-field v-model="light.shadow.mapSize.height" type="number" label="Shadow heiht"/>
          <v-text-field v-model="light.castShadow" type="number" label="Cast shadow"/>
          <v-text-field v-model="light.distance" type="number" label="Distance"/>
          <v-text-field v-model="light.decay" type="number" label="Decay"/>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
  </div>
</template>

<script>

export default {
  name: 'Menu',
  props:['allLights'],
  data() {
    return {
      selectedColors: [],
    };
  },
  methods: {
    updateColorForOneLight(index, light) {
      this.$emit('updateColor', this.selectedColors[index], light)
    }
  }
}
</script>

<style scoped>

</style>
