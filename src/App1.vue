<template>
  <div id="map" class="map"></div>
  <div class="btns">
    <div class="btn" @click="open">开启绘制多边形</div>
    <div class="btn" @click="close">关闭绘制多边形</div>
  </div>
</template>

<script setup>
import { onMounted } from "vue";
import { Scene } from "@antv/l7";
import { GaodeMapV2 } from "@antv/l7-maps";

let mouseTool = null;
let isOpen = false;

const open = () => {
  if (isOpen) return;
  isOpen = true;

  mouseTool.polygon({
    fillColor: "#00b0ff",
    strokeColor: "#80d8ff",
  });
};

const close = () => {
  if (!isOpen) return;
  isOpen = false;

  mouseTool.close(true);
};

const createScene = ($el) => {
  // eslint-disabled
  const map = new window.AMap.Map($el, {
    mapStyle: "amap://styles/whitesmoke",
    viewMode: "3D",
    defaultCursor: "pointer", // 鼠标手势
    doubleClickZoom: false, // 双击放大地图
    expandZoomRange: true, // 扩大地图最大缩放范围
    zoom: 12.5, // 初始缩放
    zooms: [10, 20], // 缩放范围
    center: [115.800471, 28.604272],
    rotation: 90,
  });

  const scene = new Scene({
    // 生成L7实例并挂载高德地图
    id: $el, // 地图容器ID
    logoVisible: false, // L7 logo是否显示,
    map: new GaodeMapV2({
      mapInstance: map,
    }),
  });

  return new Promise(() => {
    scene.on("loaded", () => {
      mouseTool = new window.AMap.MouseTool(scene.map);
    });
  });
};

onMounted(() => {
  createScene("map");
});
</script>

<style>
html,
body,
#app,
#map {
  width: 100%;
  height: 100%;
}
</style>

<style scoped>
.btns {
  position: absolute;
  top: 20px;
  left: 50px;
  z-index: 2;
  display: flex;
}
.btn {
  padding: 5px;
  background-color: #ccc;
  border: 1px solid #bbb;
  margin: 0 10px;
  cursor: pointer;
}
</style>
