<template>
  <div>
    <!-- GIS / BIM 切换组件 -->
    <el-card class="box-card">
      <div class="title">GIS / BIM 切换组件</div>
      <hr />
      <div class="button" @click="gbSwitch(false)">切换 GIS</div>
      <div class="button" @click="gbSwitch()">切换 BIM</div>
    </el-card>
  </div>
</template>

<script type="text/javascript">
import * as Cesium from 'cesium'

export default {

  methods: {
    init (modelId, lockAxiz) {
      this.modelId = modelId;
      this.lockAxiz = lockAxiz;
    },
    gbSwitch (bool = true) {

      // 开启地下模式
      uniCore.model.undergroundMode(bool);

      if (bool) {
        uniCore.viewer.scene.backgroundColor = Cesium.Color.fromCssColorString("#b9d3ee");
        uniCore.viewer.terrainProvider = null;

        // 只留该模型显示，其他全部隐藏
        uniCore.model.setPrimitivesShow(this.modelId, false, false)

        // // 信息树只留该模型
        // window.nodesList = window.nodesList?.filter(e =>
        //   e.id === this.modelId
        // )

      } else {
        uniCore.viewer.scene.backgroundColor = null;
        uniCore.viewer.terrainProvider = window.terrainProvider;

        // 还原所有模型显示
        uniCore.model.setPrimitivesShow('', true)

        // // 信息树还原所有模型
        // window.nodesList = window.nodesListSaved;

      }
      // 打开视角锁定
      uniCore.position.lockTo(uniCore.viewer, bool, this.lockAxiz);

    }

  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
::v-deep .el-card__body {
  padding: 20px 0px 0 0px;
}
::v-deep .box-card {
  position: absolute;
  top: 3%;
  left: 3%;
  width: 300px;
  z-index: 1;
  background: rgb(26 26 26 / 83%);
  border: 1px solid rgba(255, 255, 255, 0.3);
  box-shadow: 0px 24px 54px 0px rgba(35, 41, 50, 0.5);
  border-radius: 15px;
  padding: 0 24px 12px 24px;
  margin-bottom: 12px;
  -webkit-backdrop-filter: blur(10px);
  backdrop-filter: blur(10px);

  .title {
    font-size: 18px;
    font-weight: bold;
    text-shadow: 1px 1px #000;
    color: #fefeff;
    display: block;
    margin-bottom: 10px;
  }

  hr {
    margin-left: 24px;
    margin-bottom: 10px;
    border: none;
    border-bottom: 1px solid #ffffff1a;
  }

  .button {
    display: inline-flex;
    margin: 5px 10px;
    color: white;
    background: #4d4d4dd1;
    border-radius: 10px;
    padding: 7px 20px;
    cursor: pointer;
    transition: 0.3s;
  }
}
</style>