---
title: three-入门学习-3D房屋全景实现
date: 2023-04-06 16:14:00
categories: 入门学习
tags: Three
---

### three-3D 房屋全景实现

###### 1. 安装 three.js

> 根据之前的粗略学习，突发想做个全景图网上学习一波

> > 3D 效果是前端绕不过的发展趋势，未来会有更多的 3D 场景的需要。

```javascript
<template>
  <div>
    3D

    <div class="three-box-one">
      <div id="three" />
    </div>
  </div>
</template>
<script setup lang="ts">
import * as THREE from 'three';
import { reactive, onMounted } from 'vue';
import house from './assets/panorama-house.webp';
let scene: THREE.Scene, mesh;
interface DataType {
  id: HTMLElement | any;
  domW: Number | any;
  domH: Number | any;
  camera: THREE.PerspectiveCamera | any;
  renderer: THREE.WebGLRenderer | any;
  mesh: THREE.Mesh | any;
  material: THREE.MeshBasicMaterial;
  // controls?: OrbitControls | any;
  onMouseDownMouseX: number;
  onMouseDownMouseY: number;
  lon: number;
  lat: number;
  onMouseDownLon: number;
  onMouseDownLat: number;
  phi: number;
  theta: number;
  isUserInteracting: Boolean;
}

const dataType: DataType = {
  id: null,
  domW: 0,
  domH: 0,
  camera: THREE.PerspectiveCamera,
  renderer: THREE.WebGLRenderer,
  mesh: THREE.Mesh,
  material: new THREE.MeshBasicMaterial(),
  // controls: OrbitControls,
  onMouseDownMouseX: 0,
  onMouseDownMouseY: 0,
  lon: 0,
  lat: 0,
  onMouseDownLon: 0,
  onMouseDownLat: 0,
  phi: 0,
  theta: 0,
  isUserInteracting: false,
};
const data = reactive(dataType);
onMounted(() => {
  data.id = document.getElementById('three');
  data.domW = window.innerWidth;
  data.domH = window.innerHeight;

  init();
});
const init = () => {
  // 构建器
  scene = new THREE.Scene();
  // 创建近大远小（透视投影）相机
  data.camera = new THREE.PerspectiveCamera(
    75,
    data.domW / data.domH,
    0.01,
    1100
  );
  // 返回一个能够表示当前摄像机所正视(拍摄)的世界空间方向的Vector3对象
  data.camera.target = new THREE.Vector3(0, 0, 0);
  // data.camera.position.z = 5;
  // 创建渲染函数
  data.renderer = new THREE.WebGLRenderer({
    antialias: true, // 模型抗锯齿
    alpha: true, // 开启背景透明
  });
  data.renderer.setClearColor(new THREE.Color(0x000000));
  data.renderer.setPixelRatio(window.devicePixelRatio);
  // 设置渲染场景大小
  data.renderer.setSize(data.domW, data.domH);
  // 将场景添加到div标签
  data.id.appendChild(data.renderer.domElement);
  // 添加灯光
  addLight();
  // 添加场景辅助线
  axisHelper();
  // 添加球体设置材质
  initSphereGeometry();
  // 添加事件监听器，配合鼠标做不同的位置变换
  addEventListenFn();
  // 刷帧渲染动画
  animate();
  // 响应屏幕改变大小函数
  onWindowResize();
};

const addLight = () => {
  // 设置环境光 环境光会均匀的照亮场景中的所有物体。
  const ambientLight = new THREE.AmbientLight('#ffffff');
  scene.add(ambientLight);
  // 设置平行光
  const light = new THREE.DirectionalLight('#ffffff');
  scene.add(light);
  // 设置点光源
  const pointLight = new THREE.PointLight('#ffffff', 0.1, 1000);
  pointLight.position.set(300, 300, 300);
  scene.add(pointLight);
};

const axisHelper = () => {
  const axes: THREE.AxesHelper = new THREE.AxesHelper(800);
  scene.add(axes);
};
const initSphereGeometry = () => {
  // 创建半径500的球体 （球缓冲几何体）
  const geometry = new THREE.SphereGeometry(500, 32, 16);
  geometry.scale(-1, 1, 1);
  // 创建材质获取材质图片鱼眼图
  data.material = new THREE.MeshBasicMaterial({
    // 加载墙壁图纸 自己网上找一个全景图片就行了
    map: new THREE.TextureLoader().load(house),
  });
  mesh = new THREE.Mesh(geometry, data.material);
  // 将球体添加到场景
  scene.add(mesh);
};
const addEventListenFn = () => {
  const _this: any = data;
  // 鼠标按下获取鼠标xy坐标转换成球体经纬度
  document.addEventListener('mousedown', onPointerStart, false);
  // 鼠标移动计算变化后的球体经纬度
  document.addEventListener('mousemove', onPointerMove, false);
  // 鼠标抬起停止跟随
  document.addEventListener('mouseup', onPointerUp, false);
  // 鼠标滚轮放大缩小摄像机目标距离
  document.addEventListener('wheel', onDocumentMouseWheel, false);
  // 移动端手指移上获取鼠标xy坐标转换成球体经纬度
  document.addEventListener('touchstart', onPointerStart, false);
  // 移动端手指移动计算变化后的球体经纬度
  document.addEventListener('touchmove', onPointerMove, false);
  // 移动端手指抬起停止跟随
  document.addEventListener('touchend', onPointerUp, false);
  // 拖拽
  document.addEventListener(
    'dragover',
    (e: any) => {
      e.preventDefault();
      e.dataTransfer.dropEffect = 'copy';
    },
    false
  );
  // 拓拽停止设置body 半透明
  document.addEventListener(
    'dragenter',
    () => {
      document.body.style.opacity = '0.5';
    },
    false
  );
  // 拖拽离开回归透明度
  document.addEventListener(
    'dragleave',
    () => {
      document.body.style.opacity = '1';
    },
    false
  );
  document.addEventListener(
    'drop',
    (e: any) => {
      e.preventDefault();
      // 读取图片为二进制码
      const reader = new FileReader();
      reader.addEventListener(
        'load',
        (es: any) => {
          // 更新材质
          _this.material.map.image.src = es.target.result;
          _this.material.needsUpdate = true;
        },
        false
      );
      reader.readAsDataURL(e.dataTransfer.files[0]);
      document.body.style.opacity = '1';
    },
    false
  );
};

const onPointerStart = (e) => {
  data.isUserInteracting = true;
  // 获取鼠标x y 坐标
  const clientX = e.clientX || e.touches[0].clientX;
  const clientY = e.clientY || e.touches[0].clientY;
  data.onMouseDownMouseX = clientX;
  data.onMouseDownMouseY = clientY;
  // 设置经纬度
  data.onMouseDownLon = data.lon;
  data.onMouseDownLat = data.lat;
};
const onPointerMove = (e) => {
  if (data.isUserInteracting) {
    const clientX = e.clientX || e.touches[0].clientX;
    const clientY = e.clientY || e.touches[0].clientY;
    data.lon = (data.onMouseDownMouseX - clientX) * 0.1 + data.onMouseDownLon;
    data.lat = (clientY - data.onMouseDownMouseY) * 0.1 + data.onMouseDownLat;
  }
};
const onPointerUp = () => {
  data.isUserInteracting = false;
};
const onDocumentMouseWheel = (e) => {
  const fov = data.camera.fov + e.deltaY * 0.05;
  data.camera.fov = THREE.MathUtils.clamp(fov, 10, 75);
  data.camera.updateProjectionMatrix();
};
const animate = () => {
  window.requestAnimationFrame(animate);
  // 更新相机 旋转场景空间
  updateFn();
};
const updateFn = () => {
  if (!data.isUserInteracting) {
    // 经度每帧更新0.1 场景自动旋转起来
    data.lon += 0.1;
  }
  data.lat = Math.max(-85, Math.min(85, data.lat));
  data.phi = THREE.MathUtils.degToRad(90 - data.lat);
  data.theta = THREE.MathUtils.degToRad(data.lon);
  // 更新相机 目标 x y z 位置
  data.camera.target.x = 500 * Math.sin(data.phi) * Math.cos(data.theta);
  data.camera.target.y = 500 * Math.cos(data.phi);
  data.camera.target.z = 500 * Math.sin(data.phi) * Math.sin(data.theta);
  // 相机拍摄目标（始终拍摄这里）
  data.camera.lookAt(data.camera.target);
  data.renderer.render(scene, data.camera);
};
const onWindowResize = () => {
  window.onresize = () => {
    data.domH = data.id.offsetHeight;
    data.domW = data.id.offsetWidth;
    data.camera.aspect = data.domW / data.domH;
    data.camera.updateProjectionMatrix();
    data.renderer.setSize(data.domW, data.domH);
  };
};
</script>
<style scoped></style>


```
