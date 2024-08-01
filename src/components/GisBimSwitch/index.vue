<template>
  <div>
    <!-- GIS / BIM 切换组件 -->
    <el-card class="box-card">
      <div
        id="move-layer"
        class="title"
        @mousedown="mousedown"
        @mouseup="mouseup"
      >
        GIS / BIM 切换组件
      </div>
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

    },

    /**
 * 鼠标与窗口拖动相关
 */
    mousedown (event, id) {
      if (document.elementFromPoint(event.clientX, event.clientY).id === 'move-layer') {
        this.selectElement = document.elementFromPoint(event.clientX, event.clientY).parentNode.parentNode;
        document.querySelectorAll('.box-card').forEach((e) => {
          e.style.zIndex = 1000;
        })
        this.selectElement.style.zIndex = 1001;
        var div1 = this.selectElement
        this.selectElement.style.cursor = 'move'
        this.isDowm = true
        var distanceX = event.clientX - this.selectElement.offsetLeft
        var distanceY = event.clientY - this.selectElement.offsetTop
        document.onmousemove = function (ev) {
          var oevent = ev || event
          div1.style.left = oevent.clientX - distanceX + 'px'
          div1.style.top = oevent.clientY - distanceY + 'px'
        }
        document.onmouseup = function () {
          document.onmousemove = null
          document.onmouseup = null
          div1.style.cursor = 'default'
        }
      }

    },
    //鼠标抬起
    mouseup () {
      this.isMove = false;
      this.selectElement = "null"
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
  transition: none;
  user-select: none;

  .title {
    font-size: 18px;
    font-weight: bold;
    color: #fefeff;
    display: block;
    margin-left: 24px;
    margin-bottom: 10px;
    user-select: none;
    overflow: hidden;
    cursor: move;
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