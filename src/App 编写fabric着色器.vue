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
    // vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
    vertexFormat: Cesium.EllipsoidSurfaceAppearance.VERTEX_FORMAT,
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
    // vertexFormat: Cesium.EllipsoidSurfaceAppearance.VERTEX_FORMAT,
  });

  // 03-设置外观
  // 编写着色器修改材质
  // https://cesium.com/downloads/cesiumjs/releases/b28/Documentation/
  let material1 = new Cesium.Material({
    fabric: {
      uniforms: {
        uTime: 0,
      },
      source: `
        czm_material czm_getMaterial(czm_materialInput materialInput)
        {
          // 生成默认的基础材质
          czm_material material = czm_getDefaultMaterial(materialInput);
          // material.diffuse = vec3(materialInput.st, 0.0);
          float strength = mod((materialInput.s-uTime) * 10.0, 1.0);
          material.diffuse = vec3(strength, 0.0, 0.0);
          return material;
        }
      `,
    },
  });
  gsap.to(material1.uniforms, {
    uTime: 1,
    duration: 10,
    repeat: -1,
    ease: "linear",
  });
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
