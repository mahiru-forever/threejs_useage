<template>
  <div class="container" ref="container"></div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader'

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

// 添加球体
const geometry = new THREE.SphereGeometry(5, 32, 32)
const loader = new RGBELoader()
loader.load('./imgs/hdr/Living.hdr', texture => {
  const material = new THREE.MeshBasicMaterial({
    map: texture
  })
  const sphere = new THREE.Mesh(geometry, material)
  sphere.geometry.scale(1, 1, -1)
  scene.add(sphere)
})

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
