<template>
  <div>
    <div id="unicoreContainer"></div>

    <!-- 机械臂控制组件开启 -->
    <RoboticArmControl ref="armControl" :step="15" :show-controls="true" />
    <!-- 机械臂控制组件结束 -->
  </div>
</template>

<script>
import { UniCore } from 'unicore-sdk'
import { config } from 'unicore-sdk/unicore.config'
import 'unicore-sdk/Widgets/widgets.css'
import RoboticArmControl from '@/components/roboticArmControl' // 机械臂控制组件

export default {
  components: {
    RoboticArmControl
  },

  // 生命周期 - 挂载完成
  mounted () {
    this.init();
  },

  methods: {
    /**
    * 通用图形引擎初始化
    */
    async init () {
      // 初始化UniCore
      let accessToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiIxNjEwMzI4My01MjBmLTQzYzktOGZiMS0wMDRhZjE0N2IyMGIiLCJpZCI6MTc1NzkyLCJpYXQiOjE3MTM3NzQ3OTh9.zU-R4MNvHr8rvn1v28PQfDImyutnpPF2lmEgGeSPckQ";
      let uniCore = new UniCore(config, accessToken);
      uniCore.init("unicoreContainer");
      let viewer = uniCore.viewer;

      // 视角初始化
      uniCore.position.buildingPosition(uniCore.viewer, [113.12380548015745, 28.250758831850005, 700], -20, -45, 1);

      // 初始化机械臂控制组件，传递初始化参数
      this.$refs.armControl.init({
        uniCore: uniCore,
        modelUrl: '../../../assets/gltf/机械臂-带层级(2).glb',
        modelPosition: [113.12098820449636, 28.256150218457687, 50],
        modelScale: 1,
        nodeNames: ["1大臂", "2大臂", "3大臂", "4双叉臂", "5小臂", "6旋转", "10夹爪1", "10夹爪2"],
        nodeAxes: ["y", "y", "z", "x", "x", "z", "x", "x"],
        nodeAngleMap: {
          "1大臂": 0,
          "2大臂": 0,
          "3大臂": 0,
          "4双叉臂": 0,
          "5小臂": 0,
          "6旋转": 0,
          "10夹爪1": 0,
          "10夹爪2": 0
        }
      });
    },

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