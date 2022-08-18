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
  // 使用entity创建矩形
  var rectangle = viewer.entities.add({
    rectangle: {
      coordinates: Cesium.Rectangle.fromDegrees(
        // 西边的经度
        90,
        // 南边维度
        20,
        // 东边经度
        110,
        // 北边维度
        30
      ),
      material: Cesium.Color.RED.withAlpha(0.5),
    },
  });

  // primivite创建矩形
  // 01-创建几何体
  let rectGeometry = new Cesium.RectangleGeometry({
    rectangle: Cesium.Rectangle.fromDegrees(
      // 西边的经度
      115,
      // 南边维度
      20,
      // 东边经度
      135,
      // 北边维度
      30
    ),
    // 距离表面高度
    height: 0,
    vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
  });

  // 02-创建几何体实例
  let instance = new Cesium.GeometryInstance({
    id: "redobj",
    geometry: rectGeometry,
    attributes: {
      color: Cesium.ColorGeometryInstanceAttribute.fromColor(
        Cesium.Color.RED.withAlpha(0.5)
      ),
    },
    vertexFormat: Cesium.EllipsoidSurfaceAppearance.VERTEX_FORMAT,
  });

  // 03-设置外观
  // let appearance = new Cesium.PerInstanceColorAppearance({
  //   flat: true,
  // });
  // type grid
  let material1 = new Cesium.Material.fromType("Grid", {
    color: Cesium.Color.AQUA.withAlpha(1),
    cellAlpha: 0.5,
    lineCount: new Cesium.Cartesian2(8, 8),
    lineThickness: new Cesium.Cartesian2(8.0, 8.0),
  });
  // let material1 = new Cesium.Material.fromType("Water", {
  //   baseWaterColor: Cesium.Color.AQUA.withAlpha(0.8),
  //   distortion: 0.25,
  //   normalMap: "./Assets/Textures/waterNormals.jpg",
  // });
  //假定几何体与地球椭球体平行，就可以在计算大量顶点属性的时候节省内存
  let appearance = new Cesium.EllipsoidSurfaceAppearance({
    material: material1,
    aboveGround: false,
    translucent: true,
  });
  // 04-图元
  let primitive = new Cesium.Primitive({
    geometryInstances: instance,
    appearance: appearance,
    show: true,
  });
  // 05-添加到viewer
  viewer.scene.primitives.add(primitive);
  viewer.camera.setView(viewer.entities);
  
  // 拾取
  var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
  handler.setInputAction(function (movement) {
    // console.log(movement);
    // scene.pick选中物体
    var pickedObject = viewer.scene.pick(movement.position);
    if (Cesium.defined(pickedObject) && typeof pickedObject.id == "string") {
      // console.log(pickedObject.id);
      let attributes = primitive.getGeometryInstanceAttributes(pickedObject.id);
      attributes.color = Cesium.ColorGeometryInstanceAttribute.toValue(
        Cesium.Color.YELLOW.withAlpha(0.5)
      );
    }
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);


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
