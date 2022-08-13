<template>
  <el-alert
    :title="`数据一共 ${total} 条`"
    type="info"
    :closable="false"
    show-icon
  >
  </el-alert>
</template>

<script>
export default {
  data() {
    return {};
  },
  props: {
    total: {
      type: Number,
      default: 0,
    },
  },
  created() {},

  methods: {},
};
</script>

<style scoped lang="less"></style>
