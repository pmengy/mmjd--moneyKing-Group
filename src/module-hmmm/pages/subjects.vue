<template>
  <div class="app-container">
    <!-- 卡片控件 -->
    <el-card class="box-card">
      <!-- 搜索框头部控件 -->
      <el-card
        class="box-search"
        shadow="never"
        :body-style="{ padding: '10px 0px 10px 20px' }"
      >
        <el-form :inline="true" :model="formInline" class="demo-form-inline">
          <el-form-item label="学科名称:" class="item">
            <el-input
              v-model="formInline.taskCode"
              placeholder="请输入"
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button size="mini" class="middle-btn">清空</el-button>
            <el-button type="primary" size="mini">搜索</el-button>
          </el-form-item>
          <div class="right">
            <el-button
              type="success"
              size="mini"
              icon="el-icon-edit"
              class="rightBtn"
              >新增学科</el-button
            >
          </div>
        </el-form>
      </el-card>
      <!-- 警示框 -->
      <el-alert title="消息提示的文案" type="info" show-icon> </el-alert>
      <!-- 表格 -->
      <Table
        :currentList="currentList"
        :tableLabel="tableLabel"
        :currentIndex="pageIndex * 10"
      ></Table>
      <!-- 底部分页 -->
      <PageTool></PageTool>
    </el-card>
  </div>
</template>

<script>
import SearchHeader from "../components/SubjectComponent/search-header.vue";
import PageTool from "../components/SubjectComponent/page-tool.vue";
import Table from "../components/SubjectComponent/table/discipline.vue";
export default {
  data() {
    return {
      // 表单数据
      currentList: [],
      taskStatusList: [], //搜索选择框数据
      formInline: {}, //搜索表单数据
      pageIndex: "", //页码
      tableLabel: [
        { label: "学科名称", width: "150", prop: "subjectName" },
        { label: "创建者", width: "150", prop: "creator" },
        { label: "创建日期", width: "150", prop: "addDate" },
        { label: "前台是否显示", width: "150", prop: "isFrontDisplay" },
        { label: "二级目录", width: "150", prop: "twoLevelDirectory.length" },
        { label: "标签", width: "150", prop: "tags" },
        { label: "题目数量", width: "150", prop: "totals" },
      ],
    };
  },

  components: { SearchHeader, PageTool, Table },

  created() {},

  methods: {
    resetDateFilter() {
      this.$refs.filterTable.clearFilter("date");
    },
    clearFilter() {
      this.$refs.filterTable.clearFilter();
    },
    formatter(row, column) {
      return row.address;
    },
    filterTag(value, row) {
      return row.tag === value;
    },
    filterHandler(value, row, column) {
      const property = column["property"];
      return row[property] === value;
    },
  },
};
</script>

<style scoped lang="less">
.app-container {
  padding: 20px;
  // 警示框
  .el-alert--info.is-light {
    margin-bottom: 20px;
  }
}
.pages {
  margin-top: 20px;
  text-align: right;
}
.right {
  // position: absolute;
  // right: 0;
  width: 55%;
  display: inline-block;
  text-align: right;
  // display: fixed;
  justify-content: flex-end;
  .rightBtn {
    height: 33px;
  }
}
</style>
