<template>
  <div id="unicoreContainer">
    <!-- GIS / BIM 切换组件窗口卡片开始 -->
    <gbSet ref="gbSetId"></gbSet>
    <!-- GIS / BIM 切换组件窗口卡片结束 -->
    <!-- BIM视图盒子组件开始 -->
    <bcSet ref="bcSetId"></bcSet>
    <!-- BIM视图盒子组件结束 -->
  </div>
</template>

<script>
import { UniCore } from 'unicore-sdk'
import { config } from 'unicore-sdk/unicore.config'
import 'unicore-sdk/Widgets/widgets.css'
import gbSet from '@/components/GisBimSwitch/index'; //GIS/BIM切换组件
import bcSet from '@/components/BimCubeSet/index.vue'; //BIM视图盒子组件

export default {

  components: {
    gbSet, bcSet
  },
  // 生命周期 - 挂载完成（可以访问DOM元素）
  mounted () {
    this.init();
  },

  // 方法集合
  methods: {

    /**
    * 通用图形引擎初始化
    */
    init () {

      // 初始化UniCore

      // 目前采用Cesium的地形&底图数据，这里配置Cesium的token
      let accessToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiIxNjEwMzI4My01MjBmLTQzYzktOGZiMS0wMDRhZjE0N2IyMGIiLCJpZCI6MTc1NzkyLCJpYXQiOjE3MTM3NzQ3OTh9.zU-R4MNvHr8rvn1v28PQfDImyutnpPF2lmEgGeSPckQ";
      // 初始化unicore
      let uniCore = new UniCore(config, accessToken);
      uniCore.init("unicoreContainer");
      window.uniCore = uniCore;
      let viewer = uniCore.viewer;

      // 视角初始化
      uniCore.position.buildingPosition(viewer, [113.12380548015745, 28.250758831850005, 700], -20, -45, 1);

      // 初始化视图盒子方法
      this.$refs.gbSetId.init(
        (pickObj, boundingSphere) => this.$refs.bcSetId.show(uniCore, uniCore.position.cartesian3_2axis(boundingSphere.center), boundingSphere.radius * 3),
        () => this.$refs.bcSetId.hide()
      );

      /**
       * 小别墅1号示例
       */
      let options = {
        id: '小别墅1号示例',
        url: '../../assets/3Dtiles/sample3_方法2_小别墅属性(1)/tileset.json',
        propertysURL: '../../assets/3Dtiles/sample3_方法2_小别墅属性(1)/01 小别墅.json'
      }
      //加载3dtiles
      uniCore.model.createTileset(options.url, options).then(cityLeft => {
        uniCore.model.changeModelPos(cityLeft, [113.12098820449636, 28.256150218457687, 130], [0, 0, 0])

        // 开启右键菜单、点击高亮、属性property
        uniCore.interact.setTilesRightClickMenu([{
          id: '小别墅1号示例',
          url: '../../assets/3Dtiles/sample3_方法2_小别墅属性(1)/tileset.json',
          propertysURL: '../../assets/3Dtiles/sample3_方法2_小别墅属性(1)/01 小别墅.json'
        }], (property) => console.log(property));
      })



      /**
         * 小别墅2号示例
         */
      uniCore.model.addGltf({
        lon: 0,
        lat: 0,
        height: 0
      }, {
        id: "小别墅2号示例",
        name: null,
        url: '../../../assets/gltf/小别墅.glb',
        scale: 1.0,
        property: null
      }).then(cityModel => {
        uniCore.model.changeModelPos(cityModel, [113.12098820449636, 28.257150218457687, 130], [90, 0, 0])
      })

      // 开启glTF模型右键交互
      uniCore.interact.setGltfRightClickMenu((property) => console.log(property));
    }
  }

}
</script>
<style scoped>
#unicoreContainer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: black;
}
</style>