<template>
  <div class="phase-page">
    <div id="cesiumContainer"
         class="map3d-contaner"></div>
    <section class="elslider-control">
      <!-- <div class="slider-demo-block">
        <span class="demonstration">xHalfAngle:</span>
        <input type="range"
               v-model="xHalfAngleDef.value"
               :max="xHalfAngleDef.max"
               :min="xHalfAngleDef.min"
               @change="xHalfAngleChange">
      </div> -->
      <div class="slider-demo-block">
        <span class="demonstration">yHalfAngle:</span>
        <input type="range"
               v-model="yHalfAngleDef.value"
               :max="yHalfAngleDef.max"
               :min="yHalfAngleDef.min"
               @change="yHalfAngleChange">
      </div>
      <div class="slider-demo-block">
        <span class="demonstration">Heading:</span>
        <input type="range"
               v-model="HeadingDef.value"
               :max="HeadingDef.max"
               :min="HeadingDef.min"
               @change="HeadingChange">
      </div>
      <div class="slider-demo-block">
        <span class="demonstration">Pitch:</span>
        <input type="range"
               v-model="PitchDef.value"
               :max="PitchDef.max"
               :min="PitchDef.min"
               @change="PitchChange">
      </div>
      <div class="slider-demo-block">
        <span class="demonstration">Roll:</span>
        <input type="range"
               v-model="RollDef.value"
               :max="RollDef.max"
               :min="RollDef.min"
               @change="RollChange">
      </div>
    </section>
  </div>
</template>
<script>
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'

export default {
  data() {
    return {
      currPosition: Cesium.Cartesian3.fromDegrees(117.224, 31.819, 128),
      roll: 0,
      pitch: 40,
      heading: 0,
      xHalfAngleDef: {
        max: 89,
        min: 1,
        value: 80 //初始值
      },
      yHalfAngleDef: {
        max: 2000000, min: 100000, value: 700000
      },

      HeadingDef: {
        max: 360, min: 0, value: 90 //初始值
      },
      PitchDef: {
        max: 360, min: 0, value: 0 //初始值
      },
      RollDef: {
        max: 360,
        min: 0,
        value: 0 //初始值
      }
    }
  },
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      const { viewer, customCesiumPlugin, graphics } = new initCesium(
        {
          cesiumGlobal: Cesium,
          containerId: 'cesiumContainer',
          viewerConfig: {
            infoBox: false,
            shouldAnimate: true,
          },
          extraConfig: {},
          MapImageryList: []
        })
      this.graphics = graphics;
      this.c_viewer = viewer
      this.customCesiumPlugin = customCesiumPlugin

      this.flyToPos()
      this.initPhaseControl()
    },
    flyToPos() {
      this.customCesiumPlugin.flyTo({
        position: {
          x: -1577100.7186109242,
          y: 5851821.270502206,
          z: 3447255.476239793
        },
        orientation: {
          heading: Cesium.Math.toRadians(78.17580384898336),
          pitch: Cesium.Math.toRadians(-29.981992162453782),
          roll: Cesium.Math.toRadians(0.005676460617140785)
        }
      })
    },
    initPhaseControl() {

      const _this = this
      // let trackedEntityTest = this.graphics.createBoxGraphics({
      //   position: Cesium.Cartesian3.fromDegrees(117.224, 31.819, 128),
      //   name: "box",
      //   box: {
      //     dimensions: new Cesium.Cartesian3(500.0, 500.0, 500.0),//尺寸，长宽高
      //     material: new Cesium.ColorMaterialProperty((new Cesium.CallbackProperty(() => {
      //       return Cesium.Color.WHITE;
      //     }, false))),
      //   }
      // })
      this.sensorEntity = this.customCesiumPlugin.createRadarPrimitive(
        {
          position: _this.currPosition,
          heading: _this.heading,
          pitch: _this.pitch,
          roll: _this.roll
        }
      )
    },
    xHalfAngleChange(e) {
      this.sensorEntity.angle = e.target.value
    },
    yHalfAngleChange(e) {
      this.sensorEntity.radius = e.target.value
    },
    HeadingChange(e) {
      this.heading = e.target.value
      this.sensorEntity._rotation.heading = Cesium.Math.toRadians(
        this.heading
      )
    },
    PitchChange(e) {
      this.pitch = e.target.value
      this.sensorEntity._rotation.pitch = Cesium.Math.toRadians(this.pitch)
    },
    RollChange(e) {
      this.roll = e.target.value
      this.sensorEntity._rotation.roll = Cesium.Math.toRadians(this.roll)
    }
  },
  beforeUnmount() {
    this.c_viewer = null
    this.customCesiumPlugin = null
  }
}
</script>
<style lang="scss" scoped>
.phase-page {
  position: relative;
  .map3d-contaner {
    width: 100%;
    height: 100%;
    overflow: hidden;
    .ctrl-group {
      position: absolute;
      top: 10px;
      right: 20px;
      z-index: 999;
      .reset-home-btn {
        color: #36a3f7;
        cursor: pointer;
      }
    }
  }
  .elslider-control {
    width: 300px;
    padding: 6px 12px;
    position: absolute;
    top: 10px;
    left: 8px;
    background: #fff;
    .slider-demo-block {
      line-height: 40px;
      display: flex;
      justify-content: space-around;
    }
  }
}
</style>
