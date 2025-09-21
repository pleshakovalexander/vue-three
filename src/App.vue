<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const container = ref<HTMLElement | null>(null)
let renderer: THREE.WebGLRenderer
let scene: THREE.Scene
let camera: THREE.PerspectiveCamera
let cube: THREE.Mesh
let door: THREE.Mesh
let capsule: THREE.Mesh
let animationId: number
let controls: OrbitControls

const scaleX = ref(1)
const scaleY = ref(1)

onMounted(() => {
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0x87ceeb)

  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)

  renderer = new THREE.WebGLRenderer({ antialias: true })

  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setAnimationLoop(() => {
    cube.rotation.x += 0.01
    cube.rotation.y += 0.01

    capsule.rotation.x += 0.01
    capsule.rotation.y += 0.01
    controls.update()

    renderer.render(scene, camera)
  })

  document.body.appendChild(renderer.domElement)

  // Cube
  const geometry = new THREE.BoxGeometry(1, 1, 1)
  const material = new THREE.MeshStandardMaterial({ color: 0xe8b11a })
  cube = new THREE.Mesh(geometry, material)
  cube.position.set(-4, 2, -5)
  scene.add(cube)

  // Capsule
  const geometry1 = new THREE.CapsuleGeometry(1, 1, 4, 8)
  const material1 = new THREE.MeshStandardMaterial({ color: 0x1ae873 })
  capsule = new THREE.Mesh(geometry1, material1)
  capsule.position.set(5, 3, -7)
  scene.add(capsule)

  //door
  const loader = new THREE.TextureLoader()
  // Create door mesh
  const doorGeometry = new THREE.BoxGeometry(2, 4, 0.1)
  const doorMaterial = new THREE.MeshStandardMaterial({
    map: loader.load('/textures/wood_diffuse.png'),
  })
  door = new THREE.Mesh(doorGeometry, doorMaterial)
  door.position.set(0, 0, -5)
  door.rotation.y = -0.4
  scene.add(door)

  // Lights
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.3)
  scene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, 1.2)
  directionalLight.position.set(5, 10, 7.5)
  directionalLight.castShadow = true
  scene.add(directionalLight)

  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.target.set(0, 0, -5)

  // Handle resize
  window.addEventListener('resize', onWindowResize)
})

onBeforeUnmount(() => {
  cancelAnimationFrame(animationId)
  window.removeEventListener('resize', onWindowResize)
  renderer.dispose()
})

function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
}

watch([scaleX, scaleY], ([x, y]) => {
  if (door) {
    door.scale.set(x, y, cube.scale.z)
  }
})
</script>

<template>
  <div class="controls">
    <label>
      Ширина:
      <input type="range" v-model.number="scaleX" min="0.9" max="2.5" step="0.1" />
      {{ scaleX.toFixed(1) }}
    </label>
    <label>
      Высота:
      <input type="range" v-model.number="scaleY" min="0.9" max="2.5" step="0.1" />
      {{ scaleY.toFixed(1) }}
    </label>
  </div>
  <div ref="container"></div>
</template>

<style scoped>
.controls {
  position: absolute;
  display: flex;
  gap: 0.5rem;
  flex-direction: column;
  margin-left: 2rem;
  margin-top: 1rem;
}

label {
  display: flex;
}
</style>
