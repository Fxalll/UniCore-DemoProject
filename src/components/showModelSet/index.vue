
<template>
  <div>
    <!-- 显隐按钮开始 -->
    <el-checkbox-group
      ref="showCheck"
      v-show="isShowCheckbox"
      v-model="checkboxGroup"
      size="mini"
      style="position: absolute; bottom: 100px; left: 0; right: 0; z-index: 999"
    >
      <el-checkbox-button
        v-for="model in modelOptions"
        :label="model"
        :key="model"
        style="margin: 1px"
        @change="showModel()"
        >{{ model }}</el-checkbox-button
      >
    </el-checkbox-group>
    <!-- 显隐按钮结束 -->
  </div>
</template>

<script type="text/javascript">

export default {
  data () {
    return {
      isShowCheckbox: true, //构件显隐窗口显示
      checkboxGroup: [], //构件显隐按钮组
      modelOptions: [], //构件组
    }
  },

  methods: {

    /**
     * 初始化
     */
    init (modelOptions) {
      this.modelOptions = modelOptions;
      this.checkboxGroup = this.modelOptions;
    },

    /**
  * 构件显隐
  */
    showModel () {
      window.uniCore.model.setPrimitivesShow(this.checkboxGroup)
    },

  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
::v-deep .el-checkbox-button__inner {
  background: #ffffff80;
  color: #00000080;
}
::v-deep .el-checkbox-button__inner:hover {
  color: #f8f8f8c5;
}
::v-deep .el-checkbox-button.is-focus .el-checkbox-button__inner {
  border-color: #ffffff4d;
}
::v-deep .el-checkbox-button.is-checked:first-child .el-checkbox-button__inner {
  border-left-color: #ffffff4d;
}
::v-deep .el-checkbox-button.is-checked .el-checkbox-button__inner {
  color: #ffffff;
  background-color: #00000080;
  border-color: #ffffff4d;
  box-shadow: -1px 0 0 0 #00000080;
  border-radius: 10px;
  backdrop-filter: blur(10px);
}
::v-deep .el-checkbox-button--mini .el-checkbox-button__inner,
::v-deep .el-checkbox-button:first-child .el-checkbox-button__inner,
::v-deep .el-checkbox-button:last-child .el-checkbox-button__inner {
  border-radius: 10px;
  backdrop-filter: blur(10px);
}
</style>
