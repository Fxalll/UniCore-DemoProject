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
     * 延迟函数辅助器
     * @param {Number} ms - 延迟毫秒数
     * @returns {Promise}
     */
    delay (ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },

    /**
     * 执行机械臂动画序列
     */
    async executeArmAnimation () {
      try {
        // 等待 2 秒后开始动画
        await this.delay(2000);

        // 依次执行旋转动画
        await this.$refs.armControl.rotateByStep(-45, "1大臂");
        await this.$refs.armControl.rotateByStep(-75, "1大臂");
        this.$refs.armControl.rotateByStep(35, "3大臂");
        this.$refs.armControl.rotateByStep(720, "6旋转");
        this.$refs.armControl.rotateByStep(75, "5小臂");
        this.$refs.armControl.rotateByStep(75, "4双叉臂");
        this.$refs.armControl.rotateByStep(15, "10夹爪1");
        this.$refs.armControl.rotateByStep(15, "10夹爪2");
        await this.delay(600);
        this.$refs.armControl.rotateByStep(-15, "10夹爪1");
        this.$refs.armControl.rotateByStep(-15, "10夹爪2");
        await this.delay(600);
        this.$refs.armControl.rotateByStep(-35, "2大臂");
        this.$refs.armControl.rotateByStep(-15, "3大臂");
        this.$refs.armControl.rotateByStep(-45, "5小臂");
        this.$refs.armControl.rotateByStep(-60, "4双叉臂");
        this.$refs.armControl.rotateByStep(90, "6旋转");

        console.log('机械臂动画序列执行完成');
      } catch (error) {
        console.error('机械臂动画执行失败:', error);
      }
    },

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
      uniCore.position.buildingPosition(uniCore.viewer, [113.20080548015745, 28.000158831850005, 35000], -20, -35, 1);

      // 初始化机械臂控制组件，传递初始化参数
      this.$refs.armControl.init({
        uniCore: uniCore,
        modelUrl: '../../../assets/gltf/机械臂-带层级(2).glb',
        modelPosition: [113.12098820449636, 28.256150218457687, 50],
        modelScale: 250,
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

      // 执行机械臂动画序列
      this.executeArmAnimation();
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