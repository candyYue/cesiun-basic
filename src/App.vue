<template>
  <div id="cesium-container" ref="cesiumContainer"></div>
</template>

<script setup>
import * as Cesium from 'cesium'
import "./Widgets/widgets.css";
import { onMounted } from 'vue'
import gsap from 'gsap'
//去除最下方AccessToken提示
Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJhMDNiODlkZS1iNzkxLTQwYmUtODRkYS1jMmU1YTQzNjRmN2MiLCJpZCI6MTA0MTQ3LCJpYXQiOjE2NjAwMjU4ODZ9.giQMZ-ryzqsofZfEf1iH5JTrk4BcqU-rxBJulFhP-S8'

// 设置静态资源路径
window.CESIUM_BASE_URL = '/';

//设置默认视角（china'
// Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
//   89.5, //西边经度
//   20.4, //南边纬度
//   110.4,//东边经度
//   61.2  //北边纬度
// )
onMounted(()=>{
  var viewer = new Cesium.Viewer('cesium-container',{
    infoBox: false, //信息窗口
    shouldAnimate: true, //初始化就加载动画
  })

  // 加载kml数据
  const czml = [
    {
      id: "document",
      name: "CZML Point - Time Dynamic",
      version: "1.0",
    },
    {
      id: "point",
      // 物体在什么时间范围可用
      availability: "2012-08-04T16:00:00Z/2012-08-04T16:05:00Z",
      position: {
        // 设置物体的起始时间
        epoch: "2012-08-04T16:00:00Z",
        // 设置了四个维度，1维是时间(秒)，2维是经度，3维是纬度，4维是高度
        cartographicDegrees: [
          0, -70, 20, 150000, 
          100, -80, 44, 150000,
          200, -90, 18, 150000, 
          300, -98, 52, 150000,
        ],
      },
      point: {
        color: {
          rgba: [255, 255, 255, 128],
        },
        outlineColor: {
          rgba: [255, 0, 0, 128],
        },
        outlineWidth: 3,
        pixelSize: 15,
      },
    },
  ];

  // 加载czml数据
  let promiseData = Cesium.CzmlDataSource.load(czml);
  promiseData.then((dataSource) => {
    console.log(dataSource);
    viewer.dataSources.add(dataSource);
    // viewer.flyTo(dataSource);
  });

  // 隐藏左下logo
  viewer.cesiumWidget.creditContainer.style.display = 'none'
})
</script>

<style lang="scss">
#cesium-container{
  width: 100vw;
  height: 100vh
}
</style>
