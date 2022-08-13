<template>
  <div class="container">
    <div v-if="!isShowSearch"></div>
    <div class="left" v-if="isShowSearch">
      <el-input v-model="input" :placeholder="placeholder"></el-input>
      <el-button size="mini" class="middle-btn">{{ text1 }}</el-button>
      <el-button type="primary" size="mini">{{ text2 }}</el-button>
    </div>
    <div class="right">
      <el-button
        type="success"
        size="mini"
        icon="el-icon-edit"
        class="rightBtn"
        >{{ rightText }}</el-button
      >
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      input: "",
    };
  },
  props: {
    // 左侧按钮1文字
    text1: {
      type: String,
    },
    // 左侧按钮2文字
    text2: {
      type: String,
    },
    // 右侧按钮文字
    rightText: {
      type: String,
    },
    // 输入框placehoder文字
    placeholder: {
      type: String,
    },
    isShowSearch: {
      type: Boolean,
      required: true,
    },
  },
  created() {},

  methods: {},
};
</script>

<style lang="less" scoped>
.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  .left {
    width: 300px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    /deep/.el-input__inner {
      width: 200px;
      height: 32px;
    }
    .el-button {
      width: 56px;
      height: 32px;
      margin: 0;
    }
    .middle-btn {
      margin: 0 10px;
    }
  }
  .right {
    // position: absolute;
    // right: 0;
    display: fixed;
    justify-content: flex-end;
    .rightBtn {
      height: 33px;
    }
  }
}
</style>
