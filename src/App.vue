<template>
  <div></div>
</template>

<script setup>
import * as THREE from 'three'
import gsap from 'gsap'

// 场景
const scene = new THREE.Scene()
// 相机
const camera = new THREE.PerspectiveCamera(
  90,
  window.innerWidth / window.innerHeight,
  1,
  1000
)
camera.position.set(0, 0, 5.5)

// 渲染器
let renderer = new THREE.WebGLRenderer({ antialias: true })
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

// 加载纹理
const textureLoader = new THREE.TextureLoader()

const texture = textureLoader.load('/bg.jpeg')
const depthTexture = textureLoader.load('/bg_depth.jpg')

const texture2 = textureLoader.load('/bg.jpeg')
const depthTexture2 = textureLoader.load('/bg_depth.jpg')

// 创建平面
// const geometry = new THREE.PlaneGeometry(window.innerWidth / 60, window.innerHeight / 60)
// console.log(window.innerWidth / 60, window.innerHeight / 60);

const geometry = new THREE.PlaneGeometry(19.2, 12)
const geometry2 = new THREE.PlaneGeometry(19.2, 12)

// 鼠标坐标
const mouse = new THREE.Vector2()

// 着色器材质
const material = new THREE.ShaderMaterial({
  uniforms: {
    uTime: { value: 0 },
    uTexture: { value: texture },
    uDepthTexture: { value: depthTexture },
    uMouse: { value: mouse }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform sampler2D uTexture;
    uniform sampler2D uDepthTexture;
    uniform vec2 uMouse;
    varying vec2 vUv;
    uniform float uTime;
    void main() {
      vec4 color = texture2D(uTexture, vUv);
      vec4 depth = texture2D(uDepthTexture, vUv);
      float depthValue = depth.r;
      float x = vUv.x + (uMouse.x+sin(uTime))*0.01*depthValue;
      float y = vUv.y + (uMouse.y+cos(uTime))*0.01*depthValue;
      vec4 newColor = texture2D(uTexture, vec2(x, y));
      gl_FragColor = newColor;
    }
  `
})

const material2 = new THREE.ShaderMaterial({
  uniforms: {
    uTime: { value: 0 },
    uTexture: { value: texture2 },
    uDepthTexture: { value: depthTexture2 },
    uMouse: { value: mouse }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform sampler2D uTexture;
    uniform sampler2D uDepthTexture;
    uniform vec2 uMouse;
    varying vec2 vUv;
    uniform float uTime;
    void main() {
      vec4 color = texture2D(uTexture, vUv);
      vec4 depth = texture2D(uDepthTexture, vUv);
      float depthValue = depth.r;
      float x = vUv.x + (uMouse.x+sin(uTime))*0.01*depthValue;
      float y = vUv.y + (uMouse.y+cos(uTime))*0.01*depthValue;
      vec4 newColor = texture2D(uTexture, vec2(x, y));
      gl_FragColor = newColor;
    }
  `
})

const plane = new THREE.Mesh(geometry, material)
plane.position.set(0, 0, 0)
const plane2 = new THREE.Mesh(geometry2, material2)
plane2.position.set(0, -12, 0)
scene.add(plane)
scene.add(plane2)

window.addEventListener('click', () => {
  gsap.to(plane.position, {
    duration: 2,
    x: 0,
    y: 12,
    z: 0,
    repeat: 0,
    ease: 'power2.inOut'
  })
  gsap.to(plane2.position, {
    duration: 2,
    x: 0,
    y: 0,
    z: 0,
    repeat: 0,
    ease: 'power2.inOut'
  })
})

let timer = false
window.addEventListener('wheel', (e) => {
  if (timer) {
    return
  }
  timer = true
  if (e.deltaY > 0) {
    gsap.to(plane.position, {
      duration: 2,
      x: 0,
      y: 12,
      z: 0,
      repeat: 0,
      ease: 'power2.inOut',
      onComplete: () => {
        timer = false
      }
    })
    gsap.to(plane2.position, {
      duration: 2,
      x: 0,
      y: 0,
      z: 0,
      repeat: 0,
      ease: 'power2.inOut'
    })
  } else {
    gsap.to(plane.position, {
      duration: 2,
      x: 0,
      y: 0,
      z: 0,
      repeat: 0,
      ease: 'power2.inOut',
      onComplete: () => {
        timer = false
      }
    })
    gsap.to(plane2.position, {
      duration: 2,
      x: 0,
      y: -12,
      z: 0,
      repeat: 0,
      ease: 'power2.inOut'
    })
  }
})

// 渲染
requestAnimationFrame(function animate() {
  material.uniforms.uMouse.value = mouse
  material.uniforms.uTime.value = performance.now() / 1000
  requestAnimationFrame(animate)
  renderer.render(scene, camera)
})

window.addEventListener('mousemove', event => {
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1
})

// 监听画面变化，更新渲染画面
window.addEventListener('resize', () => {
  //   console.log("画面变化了");
  // 更新摄像头
  camera.aspect = window.innerWidth / window.innerHeight
  //   更新摄像机的投影矩阵
  camera.updateProjectionMatrix()

  //   更新渲染器
  renderer.setSize(window.innerWidth, window.innerHeight)
  //   设置渲染器的像素比
  renderer.setPixelRatio(window.devicePixelRatio)
})
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

canvas {
  width: 100vw;
  height: 100vh;
  display: block;
  position: fixed;
  top: 0;
  left: 0;
}
</style>
