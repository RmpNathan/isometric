<template>
  <v-app>
    <v-main>
      <div ref="canvasContainer" class="canvas-container"></div>
      <div v-if="showMenu" class="menu">
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
    </v-main>
  </v-app>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';

export default {
  name: 'Scene',
  data() {
    return {
      showMenu: false,
      selectedColor: '#FF0000',
      selectedColors: [],
      allLights: [],
    };
  },
  mounted() {
    window.addEventListener('keydown', this.toggleMenu);
    this.initScene();
    this.loadModel();
    this.addCameraControls();
    this.animate();
  },
  methods: {
    updateColorForOneLight(index, light) {
      const color = new THREE.Color();
      color.set(this.selectedColors[index]); // Rouge
      light.color = color
    },
    updateColor () {
      this.scene.traverse((object) => {
        // Vérification si l'objet est une lumière
        if (object instanceof THREE.Light) {
          console.log("Lumière détectée :", object);
          // Faites clignoter la lumière en utilisant une animation basée sur le temps
          const light = object;
          const color = new THREE.Color();
          color.set(this.selectedColor); // Rouge
          light.color = color
        }
      });
    },
    toggleMenu(event) {
      if (event.keyCode === 120) { // 120 correspond au code de la touche F9
        this.showMenu = !this.showMenu;
      }
    },
    initScene() {
      this.scene = new THREE.Scene();

      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.$refs.canvasContainer.appendChild(this.renderer.domElement);

      this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
      this.camera.position.z = 5;
      this.camera.position.y = 5;
    },
    loadModel() {
      const loader = new GLTFLoader();
      loader.load('/assets/testlight/room-light.gltf', (gltf) => {
        this.model = gltf.scene;
        this.scene.add(this.model);
        // Parcours des objets de la scène
        this.scene.traverse((object) => {
          // Vérification si l'objet est une lumière
          if (object instanceof THREE.Light) {
            console.log("Lumière détectée :", object);
            const light = object;
            // light.intensity = 2; // Double l'intensité de la lumière
            const color = new THREE.Color();
            color.set(this.selectedColor); // Rouge
            light.color = color
            this.allLights.push(light)
            this.selectedColors = this.allLights.map(() => this.selectedColor);
          }
        });
      });
    },
    addCameraControls() {
      const controls = new OrbitControls(this.camera, this.renderer.domElement);
      controls.enableDamping = true;
      controls.dampingFactor = 0.05;
      controls.screenSpacePanning = false;
      controls.enableKeys = true;
      controls.keys = {
        LEFT: 'ArrowLeft', //left arrow
        UP: 'ArrowUp', // up arrow
        RIGHT: 'ArrowRight', // right arrow
        BOTTOM: 'ArrowDown' // down arrow
      };
      controls.update();
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
  },
}
</script>

<style scoped>
.canvas-container {
  width: 100%;
  height: 100vh;
}
.menu {
  position: fixed;
  top: 0;
  right: 0;
  width: 300px;
  height: 100vh;
  background-color: #f2f2f2;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: scroll;
}

.menu-title {
  font-size: 20px;
  font-weight: bold;
}
</style>
