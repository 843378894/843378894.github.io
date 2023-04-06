---
title: three-入门学习-简单的3D实现
date: 2023-04-06 11:45:00
categories: 入门学习
tags: Three
---

### three-入门学习

###### 1. 安装 three.js

> npm install three 安装好之后复制如下代码就能跑起来
>
> > 3D 效果是前端绕不过的发展趋势，未来会有更多的 3D 场景的需要。

```javascript

<template>
  <div>3D</div>
</template>
<script setup lang="ts">
import * as THREE from 'three';

//创建一个三维场景Scene 构造器，相当于你需要放东西的盒子
const scene = new THREE.Scene();
// 创建一个相机 PerspectiveCamera 透明相机，相当于一个投影仪，投影出3D效果
// PerspectiveCamera( fov : Number, aspect : Number, near : Number, far : Number )
// fov — 摄像机视锥体垂直视野角度
// aspect — 摄像机视锥体长宽比
// near — 摄像机视锥体近端面
// far — 摄像机视锥体远端面
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);

// 创建你的渲染器，用来渲染你定义的图形动画
const renderer = new THREE.WebGLRenderer();
// 设置渲染图形的大小
renderer.setSize(window.innerWidth, window.innerHeight);
// 添加元素
document.body.appendChild(renderer.domElement);

//  定义立方体图形
const geometry = new THREE.BoxGeometry(1, 1, 1);
// 构建立方体的材质和颜色
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
// 网格内的物体 构成定义的立方体图形，并且使用什么样的材质
const cube = new THREE.Mesh(geometry, material);
// 放在环境中
scene.add(cube);
// 坐标轴的位置
camera.position.z = 5;

function animate() {
  // 频率刷新 切换的时候会停止
  requestAnimationFrame(animate);
  // 物体旋转的大小
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  // 图形渲染函数 传入场景和相机
  renderer.render(scene, camera);
}

animate();
</script>
<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>

```
