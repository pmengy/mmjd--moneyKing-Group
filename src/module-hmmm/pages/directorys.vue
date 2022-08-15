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
          <el-form-item label="目录名称:" class="item">
            <el-input
              v-model="formInline.taskCode"
              placeholder="请输入"
            ></el-input>
          </el-form-item>
          <el-form-item label="状态:" class="item">
            <el-select v-model="formInline.status" placeholder="请选择">
              <el-option
                v-for="item in taskStatusList"
                :key="item.statusId"
                :label="item.statusName"
                :value="item.statusId"
              ></el-option>
            </el-select>
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
              @click="labelVisible = true"
              >新增目录</el-button
            >
          </div>
        </el-form>
      </el-card>
      <!-- 警示框 -->
      <el-alert title="消息提示的文案" type="info" show-icon :closable="false">
      </el-alert>
      <!-- 表格 -->
      <Table
        :currentList="currentList"
        :tableLabel="tableLabel"
        :currentIndex="pageIndex * 10"
      ></Table>
      <!-- 底部分页 -->
      <PageTool></PageTool>
    </el-card>
    <!-- 弹框 -->
    <el-dialog title="收货地址" :visible.sync="labelVisible" width="25%">
      <el-form :model="labelform" :rules="addRules">
        <el-form-item label="所属学科" label-width="100px">
          <el-select v-model="labelform.region" placeholder="请选择活动区域">
            <el-option
              v-for="item in tasklabelList"
              :key="item.statusId"
              :label="item.statusName"
              :value="item.statusId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录名称" prop="addName" label-width="100px">
          <el-input
            class="inputone"
            v-model="labelform.name"
            autocomplete="off"
            placeholder="请输入目录名称"
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="labelVisible = false">取 消</el-button>
        <el-button type="primary" @click="labelVisible = false"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import SearchHeader from "../components/SubjectComponent/search-header.vue";
import PageTool from "../components/SubjectComponent/page-tool.vue";
import Table from "../components/SubjectComponent/table/directory.vue";
export default {
  data() {
    return {
      // 表单数据
      currentList: [],
      taskStatusList: [], //搜索选择框数据
      formInline: {}, //搜索表单数据
      pageIndex: "", //页码
      labelVisible: false, //新增弹窗
      labelform: {}, //新增数据
      tableLabel: [
        { label: "所属学科", width: "180", prop: "subjectName" },
        { label: "目录名称", width: "180", prop: "directoryName" },
        { label: "创建者", width: "180", prop: "creator" },
        { label: "创建日期", width: "180", prop: "addDate" },
        { label: "面试题数量", width: "180", prop: "totals" },
        { label: "状态", width: "180", prop: "state" },
      ],
      // 表单效验
      addRules: {
        addName: [{ required: true, message: "请输入", trigger: "blur" }],
      },
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
  width: 35%;
  display: inline-block;
  text-align: right;
  // display: fixed;
  justify-content: flex-end;
  .rightBtn {
    height: 33px;
  }
}
.el-select {
  display: block;
}
</style>
