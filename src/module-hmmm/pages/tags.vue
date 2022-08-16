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
          <el-form-item label="标签名称:" class="item">
            <el-input
              v-model="formInline.tagName"
              placeholder="请输入"
            ></el-input>
          </el-form-item>
          <el-form-item label="状态:" class="item">
            <el-select v-model="formInline.state" placeholder="请选择">
              <el-option
                v-for="item in taskStatusList"
                :key="item.statusId"
                :label="item.statusName"
                :value="item.statusId"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button size="mini" class="middle-btn" @click.native="empty"
              >清空</el-button
            >
            <el-button type="primary" size="mini" @click.native="search"
              >搜索</el-button
            >
          </el-form-item>
          <div class="right">
            <el-button
              type="success"
              size="mini"
              icon="el-icon-edit"
              class="rightBtn"
              @click="labelVisible = true"
              >新增标签</el-button
            >
          </div>
        </el-form>
      </el-card>
      <!-- 警示框 -->
      <el-alert
        :title="`数据一共有${total}条`"
        type="info"
        show-icon
        :closable="false"
      >
      </el-alert>
      <!-- 表格 -->
      <Table
        :currentList="currentList"
        :tableLabel="tableLabel"
        :currentIndex="pageIndex * 10"
      ></Table>
      <!-- 底部分页 -->
      <PageTool
        :total="total"
        :paginationPage="page.page"
        :paginationPagesize="page.pagesize"
        @pageChange="pageChange"
        @pageSizeChange="pageSizeChange"
      ></PageTool>
    </el-card>
    <!-- 弹框 -->
    <el-dialog title="收货地址" :visible.sync="labelVisible" width="25%">
      <el-form :model="labelform" :rules="addRules">
        <el-form-item label="所属学科" label-width="100px">
          <el-select v-model="labelform.region" placeholder="请选择">
            <el-option
              v-for="item in tasklabelList"
              :key="item.statusId"
              :label="item.statusName"
              :value="item.statusId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签名称" prop="addName" label-width="100px">
          <el-input
            class="inputone"
            v-model="labelform.name"
            autocomplete="off"
            placeholder="请输入标签名称"
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
import Table from "../components/SubjectComponent/table/Thelabel.vue";
import { list, simple, add } from "@/api/hmmm/tags";
export default {
  data() {
    return {
      // 表单数据
      currentList: [],
      taskStatusList: [
        { statusId: 0, statusName: "禁用" },
        { statusId: 1, statusName: "启用" },
      ], //搜索选择框数据
      tasklabelList: [], //新增选择框数据
      formInline: {
        tagName: "",
        state: "",
      }, //搜索表单数据
      pageIndex: "", //页码
      labelVisible: false, //新增弹窗
      labelform: {}, //新增数据
      total: "", //总数
      page: {
        page: 1, //当前页数
        pagesize: 10, //每页展示的条数
      },
      tableLabel: [
        { label: "所属学科", width: "210", prop: "subjectName" },
        { label: "标签名称", width: "210", prop: "tagName" },
        { label: "创建者", width: "210", prop: "username" },
        { label: "创建日期", width: "210", prop: "addDate" },
        { label: "状态", width: "210", prop: "state" },
      ],
      // 表单效验
      addRules: {
        addName: [{ required: true, message: "请输入", trigger: "blur" }],
      },
    };
  },

  components: { SearchHeader, PageTool, Table },

  created() {
    this.sunjectList();
  },

  methods: {
    // 列表获取
    // 学科列表获取
    async sunjectList(params) {
      const res = await list(params);
      this.currentList = res.data.items;
      this.total = res.data.counts;
      console.log(res);
    },
    // 根据页数跳转
    pageChange(val) {
      this.page.page = val;
      this.sunjectList(this.page);
    },
    // 根据每页多少请求
    pageSizeChange(val) {
      this.page.pagesize = val;
      this.sunjectList(this.page);
    },
    // 搜索工单
    async search() {
      if (this.formInline.tagName == "" && this.formInline.state == "") {
        await this.sunjectList();
      } else {
        await this.sunjectList(this.formInline);
      }
    },
    // 清空搜索表单
    empty() {
      this.formInline = {
        tagName: "",
        state: "",
      };
    },
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
