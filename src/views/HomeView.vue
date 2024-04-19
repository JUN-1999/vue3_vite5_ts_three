<script setup lang="ts">
import { MyThree } from "@/utils/three";
import * as THREE from "three";
import { onMounted, onBeforeUnmount } from "vue";

let three: MyThree;
let PI = Math.PI;

onMounted(() => {
  three = new MyThree(document.querySelector("#three-box") as HTMLElement);
  // 添加天空图
  addSky();
  // 添加地面
  addPlane();
  // 添加光线
  addLight();
  // 添加一个物体
  addBox();
});
onBeforeUnmount(() => {
  three.destroyThree();
});

// 添加地面
const addPlane = () => {
  const plane = new THREE.PlaneGeometry(50, 50);
  const planeMaterial = new THREE.MeshStandardMaterial({
    color: "#ce9178",
    side: THREE.DoubleSide,
  });
  const planeBox = new THREE.Mesh(plane, planeMaterial);
  planeBox.receiveShadow = true; // 接收阴影
  planeBox.rotateX(PI / 2);
  three.scene.add(planeBox);
};
// 添加天空图
const addSky = () => {
  let skyTexture = three.textureLoader.load("/three/environment_map.jpg");
  skyTexture.colorSpace = THREE.SRGBColorSpace;
  const sky = new THREE.SphereGeometry(1000, 1000, 1000);
  const skyMaterial = new THREE.MeshBasicMaterial({
    map: skyTexture,
    side: THREE.BackSide,
  });
  const skyBox = new THREE.Mesh(sky, skyMaterial);
  three.scene.add(skyBox);
};
// 添加光线
const addLight = () => {
  // 创建一个环境光
  // const light = new THREE.AmbientLight(0x404040, 100); // 柔和的白光
  // three.scene.add(light);

  // 创建一段平行光
  const directionalLight = new THREE.DirectionalLight(0xffffff, 10);
  directionalLight.castShadow = true;
  directionalLight.position.set(20, 20, 20);
  three.scene.add(directionalLight);

  let time = 0;

  // const trailLength = 999; // 轨迹长度，即点的数量
  // const lightTrail: any[] = []; // 用于存储平行光位置的数组
  // const trailGeometry = new THREE.BufferGeometry();
  // // 创建一个简单的矩形. 在这里我们左上和右下顶点被复制了两次。
  // // 因为在两个三角面片里，这两个顶点都需要被用到。
  // const trailVertices = new Float32Array(trailLength * 3);
  // // 设置初始顶点位置
  // for (let i = 0; i < trailLength; i++) {
  //   trailVertices[i * 3] = 0; // x
  //   trailVertices[i * 3 + 1] = 0; // y
  //   trailVertices[i * 3 + 2] = 0; // z
  // }

  // // itemSize = 3 因为每个顶点都是一个三元组。
  // trailGeometry.setAttribute(
  //   "position",
  //   new THREE.BufferAttribute(trailVertices, 3)
  // );
  // const trailMaterial = new THREE.PointsMaterial({ color: 0xff0000 });
  // const trailMesh = new THREE.Points(trailGeometry, trailMaterial);
  // three.scene.add(trailMesh);

  three.AnimatorsGroup.push(() => {
    // 模拟太阳东升西落 [0-24]
    time = (time + 0.01) % 24;

    // 将时间转换为 [0, 2π] 的角度值（以弧度为单位）
    // 24小时 对应 2π 弧度 (360度)
    let angle = (time / 12) * Math.PI * 2;

    // 计算 x 的值，范围从 -10 到 10
    // 使用正弦函数将角度转换为 [-1, 1]，然后映射到 [-10, 10]
    let x = 10 * Math.sin(angle);

    // 计算 y 的值，范围从 0 到 10
    // 我们希望在 x 从 -10 到 0 时 y 从 0 增加到 10，在 x 从 0 到 10 时 y 从 10 减少到 0
    // 这可以通过计算 x 的绝对值的正弦函数，然后根据 x 的符号调整 y 的值
    let y = 10 * (1 - Math.abs(Math.sin(angle / 2)));

    directionalLight.position.set(x, y, 20);

    // // 将新位置添加到轨迹数组中
    // lightTrail.push({ x, y, z: 20 });

    // // 如果轨迹数组超过了预设的长度，移除旧的位置
    // if (lightTrail.length > trailLength) {
    //   lightTrail.shift();
    // }

    // // 更新顶点数组
    // for (let i = 0; i < trailLength; i++) {
    //   if (i <= lightTrail.length) {
    //     trailVertices[i * 3] = lightTrail[i] ? lightTrail[i].x : 0;
    //     trailVertices[i * 3 + 1] = lightTrail[i] ? lightTrail[i].y : 0;
    //     trailVertices[i * 3 + 2] = lightTrail[i] ? lightTrail[i].z : 0;
    //   } else {
    //     trailVertices[i * 3] = 0;
    //     trailVertices[i * 3 + 1] = 0;
    //     trailVertices[i * 3 + 2] = 0;
    //   }
    // }

    // // 更新 BufferAttribute 以反映顶点数据的变化
    // trailGeometry.setAttribute(
    //   "position",
    //   new THREE.BufferAttribute(trailVertices, 3)
    // );
  });

  const directionalLightHelper = new THREE.DirectionalLightHelper(
    directionalLight,
    5
  );
  three.scene.add(directionalLightHelper);
};

// 添加物体
const addBox = () => {
  let x = 3,
    y = 3,
    z = 3;
  let movex = x / 2 + 5,
    movey = y / 2 + 0.1 + 3,
    movez = z / 2 + 3;
  const box = new THREE.BoxGeometry(x, y, z, 10, 10, 10);
  const boxMaterial = new THREE.MeshStandardMaterial({
    color: "#de9c00",
  });
  const boxBox = new THREE.Mesh(box, boxMaterial);
  boxBox.castShadow = true;
  boxBox.position.set(movex, movey, movez);
  three.scene.add(boxBox);
};
</script>

<template>
  <main>
    <div class="" id="three-box"></div>
  </main>
</template>
<style lang="scss" scoped>
#three-box {
  width: 90vw;
  height: 90vh;
  background-color: skyblue;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
</style>
