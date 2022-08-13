<template>
  <div class="app-container">
    <!-- 卡片控件 -->
    <el-card class="box-card">
      <!-- 搜索框头部控件 -->
      <search-header
        text1="清空"
        text2="搜索"
        rightText="新增用户"
        placeholder="根据用户名搜索"
        :isShowSearch="true"
      />
      <!-- 警示框 -->
      <el-alert title="消息提示的文案" type="info" show-icon> </el-alert>
      <!-- 表格 -->
      <el-table ref="filterTable" :data="tableData" style="width: 100%">
        <el-table-column
          prop="date"
          label="序号"
          width="180"
          type="index"
          align="“”center"
        >
        </el-table-column>
        <el-table-column prop="name" label="邮箱" width="180">
        </el-table-column>
        <el-table-column prop="address" label="联系电话" :formatter="formatter">
        </el-table-column>
        <el-table-column prop="tag" label="用户名" width="100">
        </el-table-column>
        <el-table-column
          prop="address"
          label="权限组名称"
          :formatter="formatter"
        >
        </el-table-column>
        <el-table-column prop="address" label="角色" :formatter="formatter">
        </el-table-column>
        <el-table-column label="操作" :formatter="formatter">
          <template>
            <el-button type="primary" icon="el-icon-edit" circle></el-button>
            <el-button type="danger" icon="el-icon-delete" circle></el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import SearchHeader from "../components/search-header.vue";
export default {
  data() {
    return {
      tableData: [
        {
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄",
          tag: "家",
        },
        {
          date: "2016-05-04",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1517 弄",
          tag: "公司",
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄",
          tag: "家",
        },
        {
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1516 弄",
          tag: "公司",
        },
      ],
    };
  },

  components: { SearchHeader },

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
</style>
