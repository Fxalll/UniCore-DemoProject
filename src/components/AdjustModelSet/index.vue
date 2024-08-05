<template>
  <div>
    <!-- 模型编辑组件 -->
    <el-card class="box-card" v-show="isShow">
      <div
        id="move-layer"
        class="title"
        @mousedown="mousedown"
        @mouseup="mouseup"
      >
        模型编辑
      </div>
      <hr />

      <div class="button mainButton" @click="selectModelFunc()">选择模型</div>
      <div class="button subButton" @click="outputData()">导出设置</div>

      <div class="block">
        <span class="demonstration">经纬度、高程数值设置</span>
        <el-input
          placeholder="请输入内容"
          v-model="lon"
          type="number"
          @input="setData"
        >
          <template slot="prepend">经度</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="lat"
          type="number"
          @input="setData"
        >
          <template slot="prepend">纬度</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="het"
          type="number"
          @input="setData"
        >
          <template slot="prepend">高程</template>
        </el-input>

        <span class="demonstration">缩放数值</span>
        <el-input
          placeholder="请输入内容"
          v-model="scales1"
          type="number"
          @input="setData"
        >
          <template slot="prepend">X</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="scales2"
          type="number"
          @input="setData"
        >
          <template slot="prepend">Y</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="scales3"
          type="number"
          @input="setData"
        >
          <template slot="prepend">Z</template>
        </el-input>

        <span class="demonstration">偏移量</span>
        <el-input
          placeholder="请输入内容"
          v-model="offsets1"
          type="number"
          @input="setData"
        >
          <template slot="prepend">X</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="offsets2"
          type="number"
          @input="setData"
        >
          <template slot="prepend">Y</template>
        </el-input>
        <el-input
          placeholder="请输入内容"
          v-model="offsets3"
          type="number"
          @input="setData"
        >
          <template slot="prepend">Z</template>
        </el-input>

        <span class="demonstration">俯仰角</span>
        <el-slider v-model="hpr1" :max="maxHpr" @input="setData"></el-slider>
        <span class="demonstration">旋转角</span>
        <el-slider v-model="hpr2" :max="maxHpr" @input="setData"></el-slider>
        <span class="demonstration">翻转角</span>
        <el-slider v-model="hpr3" :max="maxHpr" @input="setData"></el-slider>
      </div>
    </el-card>
  </div>
</template>

<script type="text/javascript">
import * as Cesium from 'cesium'

export default {

  data () {
    return {
      isShow: false,
      isSelectModel: false,
      lon: 0,
      lat: 0,
      het: 0,
      scales1: 0,
      scales2: 0,
      scales3: 0,
      offsets1: 0,
      offsets2: 0,
      offsets3: 0,
      hpr1: 0,
      hpr2: 0,
      hpr3: 0,
      maxHpr: 360
    }
  },

  methods: {

    init (uniCore) {
      window.uniCore = uniCore;
      this.isShow = true;
    },

    selectModelFunc () {

      let that = this;
      this.isSelectModel = false;
      this.selectModel = null;

      this.$message(
        { message: "请点击所需编辑的模型。" }
      )
      const handler = new Cesium.ScreenSpaceEventHandler(window.viewer.scene.canvas);

      handler.setInputAction(function (e) {
        const pickObj = viewer.scene.pick(e.position);
        if (!!pickObj) {
          that.isSelectModel = true;
          that.selectModel = pickObj.primitive;

          let axis = window.uniCore.position.cartesian3_2axis(pickObj.primitive.boundingSphere.center)

          that.lon = axis[0];
          that.lat = axis[1];
          that.het = axis[2];
          that.scales1 = that.scales2 = that.scales3 = 1;
          that.offsets1 = that.offsets2 = that.offsets3 = 0;
          that.hpr1 = that.hpr2 = that.hpr3 = 0;

          that.setData();

          handler.removeInputAction(Cesium.ScreenSpaceEventType.LEFT_CLICK)//移除事件
        }
      }, Cesium.ScreenSpaceEventType.LEFT_CLICK);

    },

    outputData () {
      this.$message(
        { message: "所需代码已在控制台打印，使用F12查看" }
      )
      console.log(`导出代码：`);
      console.log(`uniCore.model.changeModelPos(tileset, [${this.lon}, ${this.lat}, ${this.het}], [${this.hpr1}, ${this.hpr2}, ${this.hpr3}], [${this.scales1}, ${this.scales2}, ${this.scales3}], [${this.offsets1}, ${this.offsets2}, ${this.offsets3}]);`);
    },

    setData () {
      try {
        if (this.isSelectModel) {

          [this.scales1, this.scales2, this.scales3] = [this.scales1, this.scales2, this.scales3].map((ele) => {
            ele = Number(ele);
            if (isNaN(ele) || ele === 0) {
              ele = 0.01;
            }
            return ele;
          })

          setTimeout(() => {
            window.uniCore.model.changeModelPos(this.selectModel, [Number(this.lon), Number(this.lat), Number(this.het)], [this.hpr1, this.hpr2, this.hpr3], [Number(this.scales1), Number(this.scales2), Number(this.scales3)], [Number(this.offsets1), Number(this.offsets2), Number(this.offsets3)])
          })


        }
      } catch (error) {
        console.log(error);
      }
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

  .block {
    padding: 0px 20px;
  }

  span {
    color: #ccc;
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

  .mainButton {
    background: #105bc5;
    font-weight: 700;
    padding: 7px 40px;
  }

  .subButton {
    background: #979797cc;
  }

  .mainButton:hover {
    background: #009fff;
    box-shadow: 0px 0px 54px 0px #009fffa8;
  }

  .subButton:hover {
    background: #d7d7d7cc;
    box-shadow: 0px 0px 54px 0px #d7d7d7a8;
  }
}
</style>