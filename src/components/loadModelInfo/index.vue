
<template>
  <div v-if="!isFinish">
    <!-- 资源加载窗口提示 -->
    <el-card class="box-card">
      <div class="title">资源加载提示</div>
      <div>
        <span class="demonstration">正在加载{{ loadName }}...</span>
        <el-progress :percentage="pgressNum" class="progress"></el-progress>
      </div>
    </el-card>
  </div>
</template>

<script type="text/javascript">

export default {
  components: {

  },
  data () {
    return {
      loadName: null,
      pgressNum: 0,
      isFinish: false,
    }
  },

  methods: {
    /**
     * 设置新值
     * @param {*} name 
     */
    setNewData (name) {
      // 让上一个项目进度条直接达到100%
      if (this.loadName != null) {
        this.pgressNum = 100;
        setTimeout(() => {
          this.loadName = name;
          this.pgressNum = 0;
          this.setLoadNum();
        }, 300)
      } else {
        this.loadName = name;
        this.pgressNum = 0;
        this.setLoadNum();
      }


    },

    /**
     * 设置安慰进度条
     */
    setLoadNum () {
      this.intervalSet = setInterval(() => {
        if (this.pgressNum < 99) {
          this.pgressNum += 3;
        } else {
          clearInterval(this.intervalSet);
        }
      }, Math.random() * Math.random() * 7000)
    },

    /**
     * 结束设置
     */
    setFinish () {
      this.pgressNum = 100;
      setTimeout(() => {
        this.isFinish = true;
      }, 500)
    }

  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
::v-deep .box-card {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%);
  z-index: 999;
  background: rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.3);
  box-shadow: 0px 24px 54px 0px rgba(35, 41, 50, 0.5);
  border-radius: 24px;
  padding: 0 24px 24px 24px;
  margin-bottom: 12px;
  -webkit-backdrop-filter: blur(10px);
  backdrop-filter: blur(10px);

  .title {
    font-size: 18px;
    font-weight: bold;
    color: #fefeff;
    display: block;
    margin-bottom: 20px;
  }

  .demonstration {
    color: #9ea3ad;
    margin: 0 10px;
  }

  .progress {
    width: 300px;
  }
}
</style>
