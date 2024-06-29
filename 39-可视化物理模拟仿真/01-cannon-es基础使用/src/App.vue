<template>
  <div></div>
</template>

<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import * as CANNON from "cannon-es";

// 初始化物理世界
const world = new CANNON.World();
// 设置重力
world.gravity.set(0, -9.82, 0);

// 初始化3D场景
const scene = new THREE.Scene();
// 初始化相机

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
camera.position.z = 3;

// 初始化渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 初始化控制器
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;

// 创建一个球体
const sphereShape = new CANNON.Sphere(0.5);

//创建一个刚体
const sphereBody = new CANNON.Body({
  //重量
  mass:1,
  //形状
  shape:sphereShape,
  //位置
  position:new CANNON.Vec3(0,5,0)
})
//将刚体添加到物理世界
world.addBody(sphereBody);


//创建一个物理世界的平面
// const planeShape = new CANNON.Plane();
const planeShape = new CANNON.Box(new CANNON.Vec3(5,0.1,5));
const planeBody = new CANNON.Body({
  // mass:0,
  shape:planeShape,
  position:new CANNON.Vec3(0,0,0),
  type:CANNON.Body.STATIC
})
// planeBody.quaternion.setFromAxisAngle(new CANNON.Vec3(1,0,0),-Math.PI/2+0.1);
//刚体添加到物理世界
world.addBody(planeBody);

//创建一个球体几何体
const sphereGeometry = new THREE.SphereGeometry(0.5,32,32);
//创建一个球体材质
const sphereMaterial = new THREE.MeshBasicMaterial({color:0x00ff00});

const sphereMesh = new THREE.Mesh(sphereGeometry,sphereMaterial);
//将网格添加到3D场景
scene.add(sphereMesh);

//创建一个平面几何体
// const planeGeometry = new THREE.PlaneGeometry(10,10);
const planeGeometry = new THREE.BoxGeometry(10,0.2,10);
//创建一个平面材质
const planeMaterial = new THREE.MeshBasicMaterial({color:0xffff00});
const planeMesh = new THREE.Mesh(planeGeometry,planeMaterial);
//旋转
// planeMesh.rotation.x = -Math.PI/2 + 0.1
//将网格添加到3D场景
scene.add(planeMesh);

//渲染
let clock = new THREE.Clock();
function animate(){
  // let del
  //2帧之间的时间
  let delta = clock.getDelta();
  world.step(1/60,delta);
  //物理世界的位置复制过去
  sphereMesh.position.copy(sphereBody.position);
  //复制角度以及其他；
  sphereMesh.quaternion.copy(sphereBody.quaternion);
  controls.update();
  renderer.render(scene,camera);
  window.requestAnimationFrame(animate);
}
animate()


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
  position: fixed;
  top: 0;
  left: 0;
}
</style>
