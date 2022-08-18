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
  const czml = [
    {
      id: "document",
      name: "box",
      version: "1.0",
    },
    {
      id: "shape1",
      name: "Blue box",
      position: {
        cartographicDegrees: [-114.0, 40.0, 300000.0],
      },
      box: {
        dimensions: {
          cartesian: [400000.0, 300000.0, 500000.0],
        },
        material: {
          solidColor: {
            color: {
              rgba: [0, 0, 255, 255],
            },
          },
        },
      },
    },
  ];

  // console.log(czml);
  // console.log(JSON.stringify(czml));

  let czmlUrl = "./box.czml";

  // 加载czml数据
  let promiseData = Cesium.CzmlDataSource.load(czmlUrl);
  promiseData.then((dataSource) => {
    console.log(dataSource);
    viewer.dataSources.add(dataSource);
    viewer.flyTo(dataSource);
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
