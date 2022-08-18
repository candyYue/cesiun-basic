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
Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(
  89.5, //西边经度
  20.4, //南边纬度
  110.4,//东边经度
  61.2  //北边纬度
)
onMounted(()=>{
  var viewer = new Cesium.Viewer('cesium-container',{
    infoBox: false, //信息窗口
  })

  // 加载kml数据
  // let kmlUrl = "./facilities1.kml";
  // let kmlDataPromise = Cesium.KmlDataSource.load(kmlUrl, {
  //   camera: viewer.scene.camera,
  //   canvas: viewer.scene.canvas,
  //   screenOverlayContainer: viewer.container,
  // });
  // console.log(kmlDataPromise);
  // kmlDataPromise.then(function (dataSource) {
  //   console.log(dataSource);
  //   viewer.dataSources.add(dataSource);
  // });
  
  // 加载kmz数据
  let kmzUrl = "./gdpPerCapita2008.kmz";
  let kmzDataPromise = Cesium.KmlDataSource.load(kmzUrl);
  console.log(kmzDataPromise);
  kmzDataPromise.then(function (dataSource) {
    console.log(dataSource);
    viewer.dataSources.add(dataSource);
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
