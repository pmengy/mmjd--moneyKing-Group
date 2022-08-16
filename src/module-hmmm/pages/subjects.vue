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
              v-model="formInline.subjectName"
              placeholder="请输入"
            ></el-input>
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
              >新增学科</el-button
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
        @compile="niceCompile"
        @direct="nicedirect"
        @tags="nicetags"
        @Dev="nicedev"
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
    <el-dialog :title="activeTitle" :visible.sync="labelVisible" width="25%">
      <el-form ref="formadd" :model="labelform" :rules="addRules">
        <el-form-item label="学科名称" prop="subjectName" label-width="100px">
          <el-input
            class="inputone"
            v-model="labelform.subjectName"
            autocomplete="off"
            placeholder="请输入学科名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="是否显示" label-width="100px">
          <el-switch
            v-model="labelform.isFrontDisplay"
            active-color="#13ce66"
            inactive-color="#ff4949"
            active-value="1"
            inactive-value="0"
          >
          </el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="CancelNew">取 消</el-button>
        <el-button type="primary" @click="newContent">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import SearchHeader from "../components/SubjectComponent/search-header.vue";
import PageTool from "../components/SubjectComponent/page-tool.vue";
import Table from "../components/SubjectComponent/table/discipline.vue";
import { list, simple, add, update, remove } from "@/api/hmmm/subjects";
export default {
  name: "sunjectList",
  data() {
    return {
      // 表单数据
      currentList: [],
      formInline: { subjectName: "" }, //搜索表单数据
      pageIndex: "", //页码
      labelVisible: false, //新增弹窗
      Datastate: true, //详情/新增判断
      labelform: {
        isFrontDisplay: "1", //表单中是否显示
        subjectName: "", //新增学科名称
      }, //新增数据
      idd: "", //修改id
      total: "", //总数
      page: {
        page: 1, //当前页数
        pagesize: 10, //每页展示的条数
      },
      tableLabel: [
        { label: "学科名称", width: "150", prop: "subjectName" },
        { label: "创建者", width: "150", prop: "username" },
        { label: "创建日期", width: "150", prop: "addDate" },
        { label: "前台是否显示", width: "150", prop: "isFrontDisplay" },
        { label: "二级目录", width: "150", prop: "isFrontDisplay" },
        { label: "标签", width: "150", prop: "tags" },
        { label: "题目数量", width: "150", prop: "totals" },
      ],
      // 表单效验
      addRules: {
        addName: [{ required: true, message: "请输入", trigger: "change" }],
      },
    };
  },

  components: { SearchHeader, PageTool, Table },
  // 计算属性
  computed: {
    activeTitle() {
      return this.Datastate ? "新增目录" : "修改目录";
    },
  },

  created() {
    this.sunjectList();
  },

  methods: {
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
      if (this.formInline.subjectName == "") {
        await this.sunjectList();
      } else {
        await this.sunjectList(this.formInline);
      }
    },
    // 清空搜索表单
    empty() {
      this.formInline = {
        subjectName: "",
      };
    },
    // 点击新增
    addNew() {
      this.labelVisible = true; //新增弹窗
      this.Datastate = true; //修改/新增判断
    },
    // 确认新增/修改
    async newContent() {
      await this.$refs.formadd.validate();
      if (this.Datastate) {
        await add({
          subjectName: this.labelform.subjectName,
          isFrontDisplay: Number(this.labelform.isFrontDisplay),
        });
        this.$message.success("添加成功");
      } else {
        await update({
          subjectName: this.labelform.subjectName,
          isFrontDisplay: Number(this.labelform.isFrontDisplay),
          id: this.idd,
        });
        this.$message.success("修改成功");
      }
      this.CancelNew();
      this.sunjectList(this.page);
      this.labelVisible = false;
    },
    // 取消新增
    CancelNew() {
      this.labelVisible = false;
      this.labelform = {
        isFrontDisplay: 0, //表单中是否显示
        subjectName: "", //新增学科名称
      }; //新增数据
    },
    // 展开修改
    niceCompile(val) {
      this.idd = val.id;
      this.labelVisible = true; //新增弹窗
      this.Datastate = false; //修改/新增判断
      console.log(val);
      this.labelform.subjectName = val.subjectName;
      this.labelform.isFrontDisplay = val.isFrontDisplay;
    },
    //跳转学科目录
    nicedirect() {},
    //跳转学科标签
    nicetags() {},
    // 删除
    async nicedev(id) {
      console.log(id);
      await remove({ id: id });
      this.$message.success("删除成功");
      this.sunjectList(this.page);
    },

    // 未知
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
.el-select {
  display: block;
}
</style>
