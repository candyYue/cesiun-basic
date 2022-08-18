<template>
  <div id="cesium-container" ref="cesiumContainer"></div>
</template>

<script setup>
import * as Cesium from 'cesium'
import "./Widgets/widgets.css";
import { onMounted } from 'vue'

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
    geocoder: false, //搜索框
    homeButton: true,//home按钮
    sceneModePicker: true,// 显示模式
    baseLayerPicker: true, //图层选择器
    navigationHelpButton: true, //帮助按钮
    animation: true,// 是否播放动画
    timeline:false, // 时间轴
    fullscreenButton:false, //全屏按钮
    terrainProvider: Cesium.createWorldTerrain({
      requestWaterMask : true, // 水纹效果
      requestVertexNormals : true // 光照效果
    }), //地形数据凹凸效果
  })

  //天安门位置
  var position = Cesium.Cartesian3.fromDegrees(116.393428, 39.90923, 100); 
  // viewer.camera.setView({
  //   // 指定相机位置
  //   destination: position,
  //   // 指定相机视角
  //   orientation: {
  //     // 指定相机的朝向,偏航角
  //     heading: Cesium.Math.toRadians(0),
  //     // 指定相机的俯仰角,0度是竖直向上,-90度是向下
  //     pitch: Cesium.Math.toRadians(-20),
  //     // 指定相机的滚转角,翻滚角
  //     roll: 0,
  //   },
  // });

  // flyto,让相机飞往某个地方
  viewer.camera.flyTo({
    destination: position,
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-20),
      roll: 0,
    },
  });


  // 添加3D建筑
  const osmBuildings = viewer.scene.primitives.add(
    new Cesium.createOsmBuildings()
  );
   // 通过按键移动相机
  document.addEventListener("keydown", (e) => {
    // console.log(e);
    // 获取相机离地面的高度
    var height = viewer.camera.positionCartographic.height;
    var moveRate = height / 100;
    if (e.key == "w") {
      // 设置相机向前移动
      viewer.camera.moveForward(moveRate);
    } else if (e.key == "s") {
      // 设置相机向后移动
      viewer.camera.moveBackward(moveRate);
    } else if (e.key == "a") {
      // 设置相机向左移动
      viewer.camera.moveLeft(moveRate);
    } else if (e.key == "d") {
      // 设置相机向右移动
      viewer.camera.moveRight(moveRate);
    } else if (e.key == "q") {
      // 设置相机向左旋转相机
      viewer.camera.lookLeft(Cesium.Math.toRadians(0.1));
    } else if (e.key == "e") {
      // 设置相机向右旋转相机
      viewer.camera.lookRight(Cesium.Math.toRadians(0.1));
    } else if (e.key == "r") {
      // 设置相机向上旋转相机
      viewer.camera.lookUp(Cesium.Math.toRadians(0.1));
    } else if (e.key == "f") {
      // 设置相机向下旋转相机
      viewer.camera.lookDown(Cesium.Math.toRadians(0.1));
    } else if (e.key == "g") {
      // 向左逆时针翻滚
      viewer.camera.twistLeft(Cesium.Math.toRadians(0.1));
    } else if (e.key == "h") {
      // 向右顺时针翻滚
      viewer.camera.twistRight(Cesium.Math.toRadians(0.1));
    }
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
