<template>
  <!-- BUG,修改过程中会直接影响后台数据展示 -->
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
              @click="addNew"
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
        @compile="niceCompile"
        @disable="nicedisable"
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
      <el-form ref="addref" :model="labelform" :rules="addRules">
        <el-form-item label="所属学科" label-width="100px">
          <el-select v-model="labelform.subjectID" placeholder="请选择">
            <el-option
              v-for="item in tasklabelList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签名称" prop="tagName" label-width="100px">
          <el-input
            class="inputone"
            v-model="labelform.tagName"
            autocomplete="off"
            placeholder="请输入标签名称"
          ></el-input>
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
import Table from "../components/SubjectComponent/table/Thelabel.vue";
import { list, add, update } from "@/api/hmmm/tags";
import { simple } from "@/api/hmmm/subjects";

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
      Datastate: true, //详情/新增判断
      labelform: {
        subjectID: "", //学科id
        tagName: "", //新增标签名称
      }, //新增数据
      idd: "", //修改id
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
        tagName: [{ required: true, message: "请输入", trigger: "blur" }],
      },
    };
  },

  components: { SearchHeader, PageTool, Table },
  // 计算属性
  computed: {
    activeTitle() {
      return this.Datastate ? "新增标签" : "修改标签";
    },
  },

  created() {
    this.sunjectList();
    this.addtag();
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
    // 获取学科简单列表
    async addtag() {
      const res = await simple();
      this.tasklabelList = res.data;
      console.log(res);
    },
    addNew() {
      this.labelVisible = true; //新增弹窗
      this.Datastate = true; //修改/新增判断
    },
    // 确认新增/修改
    async newContent() {
      await this.$refs.addref.validate();
      if (this.Datastate) {
        await add(this.labelform);
        this.$message.success("添加成功");
      } else {
        await update(this.labelform);
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
        subjectID: "", //学科id
        tagName: "", //新增标签名称
      }; //新增数据
    },
    // 展开修改
    niceCompile(val) {
      this.idd = val.id;
      this.labelVisible = true; //新增弹窗
      this.Datastate = false; //修改/新增判断
      console.log(val);
      this.labelform = val;
    },
    //禁用
    nicedisable() {},
    // 删除
    async nicedev(id) {
      console.log(id);
      await remove({ id: id });
      this.$message.success("删除成功");
      this.sunjectList(this.page);
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
