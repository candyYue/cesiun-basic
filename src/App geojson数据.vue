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
  // 加载geojson数据
  let dataGeo = Cesium.GeoJsonDataSource.load(
    "https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json",
    {
      stroke: Cesium.Color.RED,
      fill: Cesium.Color.SKYBLUE.withAlpha(0.5),
      strokeWidth: 4,
    }
  );
  // console.log(dataGeo);
  // viewer.dataSources.add(dataGeo);

  dataGeo.then((dataSources) => {
    viewer.dataSources.add(dataSources);
    let entities = dataSources.entities.values;
    entities.forEach((entity, i) => {
      entity.polygon.material = new Cesium.ColorMaterialProperty(
        Cesium.Color.fromRandom({
          alpha: 1,
        })
      );
      entity.polygon.outline = false;
      let randomNum = parseInt(Math.random() * 5);
      entity.polygon.extrudedHeight = 100000 * randomNum;
    });
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
