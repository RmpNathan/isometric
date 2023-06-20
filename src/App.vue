

<!--<template>-->
<!--  <div id="app">-->
<!--    <div ref="canvasContainer" class="canvas-container"></div>-->
<!--  </div>-->
<!--</template>-->

<!--<script>-->
<!--import * as THREE from 'three';-->
<!--import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';-->
<!--import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';-->

<!--export default {-->
<!--  name: 'App',-->
<!--  mounted() {-->
<!--    this.initScene();-->
<!--    this.loadModel();-->
<!--    this.addCameraControls();-->
<!--    this.animate();-->
<!--  },-->
<!--  methods: {-->
<!--    initScene() {-->
<!--      this.scene = new THREE.Scene();-->

<!--      this.renderer = new THREE.WebGLRenderer();-->
<!--      this.renderer.setSize(window.innerWidth, window.innerHeight);-->
<!--      this.$refs.canvasContainer.appendChild(this.renderer.domElement);-->

<!--      this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);-->
<!--      this.camera.position.z = 5;-->
<!--      this.camera.position.y = 5-->
<!--    },-->
<!--    loadModel() {-->
<!--      const loader = new GLTFLoader();-->
<!--      loader.load('/assets/testlight/room-light.gltf', (gltf) => {-->
<!--        this.model = gltf.scene;-->
<!--        this.scene.add(this.model);-->
<!--        // Parcours des objets de la scène-->
<!--        this.scene.traverse(function (object) {-->
<!--          // Vérification si l'objet est une lumière-->
<!--          if (object instanceof THREE.Light) {-->
<!--            console.log("Lumière détectée :", object);-->
<!--          }-->
<!--        });-->
<!--      });-->
<!--    },-->
<!--    addCameraControls() {-->
<!--      const controls = new OrbitControls(this.camera, this.renderer.domElement);-->
<!--      controls.enableDamping = true;-->
<!--      controls.dampingFactor = 0.05;-->
<!--      controls.screenSpacePanning = false;-->
<!--      controls.enableKeys = true;-->
<!--      controls.keys = {-->
<!--        LEFT: 'ArrowLeft', //left arrow-->
<!--        UP: 'ArrowUp', // up arrow-->
<!--        RIGHT: 'ArrowRight', // right arrow-->
<!--        BOTTOM: 'ArrowDown' // down arrow-->
<!--      }-->
<!--      controls.update();-->
<!--    },-->
<!--    animate() {-->
<!--      requestAnimationFrame(this.animate);-->
<!--      this.renderer.render(this.scene, this.camera);-->
<!--    },-->
<!--  },-->
<!--};-->
<!--</script>-->

<!--<style>-->
<!--body {-->
<!--  margin: 0;-->
<!--  overflow: hidden;-->
<!--}-->
<!--.canvas-container {-->
<!--  width: 100%;-->
<!--  height: 100vh;-->
<!--}-->
<!--</style>-->
<template>
  <v-app>
    <v-main>
      <div ref="canvasContainer" class="canvas-container"></div>
      <div v-if="showMenu" class="menu">
        <h1 class="menu-title">Controle</h1>
        <v-color-picker v-model="selectedColor" @input="updateColor"></v-color-picker>
      </div>
    </v-main>
  </v-app>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
export default {
  name: 'App',
  data() {
    return {
      showMenu: false,
      selectedColor: '#FF0000'
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
    updateColor () {
      console.log('color change : ', this.selectedColor)
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

            // Faites clignoter la lumière en utilisant une animation basée sur le temps
            const light = object;
            light.castShadow = true; // Active la projection d'ombres pour la lumière
            light.shadow.mapSize.width = 1024; // Largeur de la carte d'ombres
            light.shadow.mapSize.height = 1024; // Hauteur de la carte d'ombres
            light.distance = 10; // La lumière commence à s'atténuer après 10 unités de distance
            // light.decay = 2; // Atténuation quadratique de la lumière
            light.intensity = 2; // Double l'intensité de la lumière
            const initialIntensity = light.intensity;
            const color = new THREE.Color();
            color.set(this.selectedColor); // Rouge
            light.color = color
            // const blinkDuration = 1000; // Durée d'un cycle de clignotement (en millisecondes)
            const blinkInterval = 500; // Intervalle entre les changements d'intensité (en millisecondes)

            let isLightOn = true; // Variable pour suivre l'état de la lumière

            const updateLight = () => {
              isLightOn = !isLightOn; // Inverse l'état de la lumière

              light.intensity = isLightOn ? initialIntensity : 0;

              setTimeout(updateLight, blinkInterval);
            };
            updateLight();
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
};
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
}
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
}

.menu-title {
  font-size: 20px;
  font-weight: bold;
}
</style>
