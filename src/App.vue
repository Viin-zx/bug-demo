<template>
  <div id="map" class="map"></div>
</template>

<script setup>
import { onMounted } from "vue";
import { Scene, LineLayer, PointLayer } from "@antv/l7";
import { GaodeMapV2 } from "@antv/l7-maps";

/**
 * 图层触发的事件跟zIndex无关，只跟插入图层先后顺序有关。
 * 1, point先add, zIndex=2; line后add, zIndex=1, 点击point跟line交界的地方，触发的是line-click事件
 * 2, line先add, zIndex=2; point后add, zIndex=1,点击line跟point交界的地方，触发的是point-click事件
 *
 */

const linePath = [
  {
    path: [
      [115.800471, 28.604272],
      [115.810471, 28.614272],
      [115.820471, 28.624272],
    ],
  },
];

const lineLayer = new LineLayer({ zIndex: 2 })
  .source(linePath, {
    parser: {
      type: "json",
      coordinates: "path",
    },
  })
  .size(6)
  .shape("line")
  .texture("02")
  .color("#25d8b7")
  .animate({
    interval: 1, // 间隔
    duration: 1, // 持续时间，延时
    trailLength: 2, // 流线长度
  })
  .style({
    lineTexture: true, // 开启线的贴图功能
    iconStep: 20, // 设置贴图纹理的间距
  });
lineLayer.on("click", () => {
  console.log("点击线");
});
lineLayer.on("contextmenu", (e) => {
  e.target.stopPropagation();
  console.log("右键点击线");
});

const pointData = [
  {
    lng: 115.810471,
    lat: 28.614272,
  },
];

const pointLayer = new PointLayer({ zIndex: 1 })
  .source(pointData, {
    parser: {
      type: "json",
      x: "lng",
      y: "lat",
    },
  })
  .shape("00")
  .size(10)
  .color("red")
  .style({
    opacity: 1,
    strokeWidth: 1,
  });
pointLayer.on("click", () => {
  console.log("点击点");
});
pointLayer.on("contextmenu", (e) => {
  e.target.stopPropagation();
  console.log("右键点击点");
});

const addLayer = (scene) => {
  // 这里顺序调换，搭配图层zIndex 会有不同效果
  scene.addLayer(lineLayer);
  scene.addLayer(pointLayer);
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
      // mapStyle: 'amap://styles/27ec5e947e2818f10f648a01488d29d2',
      // defaultCursor: 'pointer', // 鼠标手势
      // doubleClickZoom: false, // 双击放大地图
      // zoom: 12.5, // 初始缩放
      // zooms: [mapOptions.minZoom, mapOptions.maxZoom], // 缩放范围
      // center: [115.800471, 28.604272],
      // rotation: 90,
      // token: '58a5dfea472932a82d9bb33c86a74f62',
      // plugin: ['AMap.DistrictSearch', 'AMap.ControlBar', 'AMap.MouseTool'] // 获取行政区域查询插件
    }),
  });

  return new Promise(() => {
    scene.on("loaded", () => {
      scene.addImage(
        "00",
        "https://gw.alipayobjects.com/zos/basement_prod/604b5e7f-309e-40db-b95b-4fac746c5153.svg"
      );
      scene.addImage(
        "02",
        "https://gw.alipayobjects.com/zos/bmw-prod/ce83fc30-701f-415b-9750-4b146f4b3dd6.svg"
      );
      addLayer(scene);
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
