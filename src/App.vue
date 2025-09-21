<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'
import * as THREE from 'three'

const container = ref<HTMLElement | null>(null)
let renderer: THREE.WebGLRenderer
let scene: THREE.Scene
let camera: THREE.PerspectiveCamera
let cube: THREE.Mesh
let capsule: THREE.Mesh
let animationId: number

onMounted(() => {
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0x87ceeb) // sky blue

  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)

  renderer = new THREE.WebGLRenderer({ antialias: true })
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setAnimationLoop(() => {
    cube.rotation.x += 0.01
    cube.rotation.y += 0.01

    capsule.rotation.x += 0.01
    capsule.rotation.y += 0.01

    renderer.render(scene, camera)
  })
  document.body.appendChild(renderer.domElement)

  // Cube
  const geometry = new THREE.BoxGeometry(1, 1, 1)
  const material = new THREE.MeshStandardMaterial({ color: 0xe8b11a })
  cube = new THREE.Mesh(geometry, material)
  scene.add(cube)

  // Capsule
  const geometry1 = new THREE.CapsuleGeometry(1, 1, 4, 8)
  const material1 = new THREE.MeshStandardMaterial({ color: 0x1ae873 })
  capsule = new THREE.Mesh(geometry1, material1)
  capsule.position.set(5, 2, -3)
  scene.add(capsule)

  // Lights
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.4) // soft global light
  scene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, 1)
  directionalLight.position.set(5, 10, 7.5)
  scene.add(directionalLight)

  camera.position.z = 5

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
</script>

<template>
  <div ref="container"></div>
</template>

<style scoped></style>
