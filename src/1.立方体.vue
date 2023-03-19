<template>
  <div class="container" ref="container"></div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

// 初始化场景
const scene = new THREE.Scene()

// 初始化相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
)
camera.position.z = 0.1

// 初始化渲染器
const renderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)

const container = ref(null)

const render = () => {
  renderer.render(scene, camera)

  requestAnimationFrame(render)
}

// 添加立方体
const geometry = new THREE.BoxGeometry(10, 10, 10)
// const material = new THREE.MeshBasicMaterial({
//   color: 0x00ff00
// })
// const cube = new THREE.Mesh(geometry, material)
// scene.add(cube)

const arr = ['4_l', '4_r', '4_u', '4_d', '4_b', '4_f']
const boxMaterials = arr.map(v => {
  const texture = new THREE.TextureLoader().load(`./imgs/living/${v}.jpg`)
  if (v === '4_u' || v === '4_d') {
    texture.rotation = Math.PI
    texture.center = new THREE.Vector2(0.5, 0.5)
  }
  return new THREE.MeshBasicMaterial({
    map: texture
    // side: THREE.DoubleSide
  })
})
const cube = new THREE.Mesh(geometry, boxMaterials)
cube.geometry.scale(1, 1, -1)
scene.add(cube)

onMounted(() => {
  // 添加控制器
  const controls = new OrbitControls(camera, container.value)
  controls.enableDamping = true

  container.value.appendChild(renderer.domElement)
  render()
})
</script>

<style>
* {
  padding: 0;
  margin: 0;
}
</style>
