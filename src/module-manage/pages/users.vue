<template>
  <div class="app-container">
    <!-- 卡片控件 -->
    <el-card
      class="box-card"
      v-loading="loading"
      element-loading-text="客官稍等，马上就到~~"
      element-loading-spinner="el-icon-loading"
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
      <!-- 搜索框头部控件 -->
      <search-header
        text1="清空"
        text2="搜索"
        rightText="新增用户"
        placeholder="根据用户名搜索"
        :isShowSearch="true"
        @searchUser="searchUser"
        @addNew="addNew"
        @clearAll="clearAll"
      />
      <!-- 警示框 -->
      <el-alert title="消息提示的文案" type="info" show-icon> </el-alert>
      <!-- 表格 -->
      <el-table ref="filterTable" :data="userData" style="width: 100%">
        <el-table-column
          label="序号"
          type="index"
          :index="indexMethod"
          width="180"
          align="center"
        >
        </el-table-column>
        <el-table-column prop="email" label="邮箱" width="180">
        </el-table-column>
        <el-table-column prop="phone" label="联系电话"> </el-table-column>
        <el-table-column prop="username" label="用户名" width="100">
        </el-table-column>
        <el-table-column prop="permission_group_title" label="权限组名称">
        </el-table-column>
        <el-table-column prop="role" label="角色"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="{ row }">
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              @click="Edit(row.id)"
              v-if="row.id !== 2"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              circle
              @click="delUser(row)"
              v-if="row.id !== 2"
            >
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <footer>
        <!-- 分页器 -->
        <page-tool
          :total="total"
          :paginationPage="queryInfo.page"
          :paginationPagesize="queryInfo.pagesize"
          @pageChange="onPageChange"
          @pageSizeChange="onPageSizeChange"
        />
      </footer>
      <!-- 新增用户弹层 -->
    </el-card>
    <AddUser
      :formBase="addUserForm"
      :text="text"
      :pageTitle="pageTitle"
      @newDataes="newDataes"
      :dialogFormVisible="dialogFormVisible"
      @closeDialog="onClose"
      :PermissionGroupsList="permissionsList"
      ref="addUser"
      :ruleInline="ruleInline"
      @clearData="clearData"
      :id="id"
    />
  </div>
</template>

<script>
import SearchHeader from "../components/search-header.vue";
import PageTool from "../components/page-tool.vue";
import AddUser from "../components/user-add.vue";
import { list, remove, detail } from "@/api/base/users";
import { simple } from "@/api/base/permissions";
export default {
  name: "manageUser",
  data() {
    return {
      dialogFormVisible: false,
      userData: [],
      queryInfo: {
        page: 1, //
        pagesize: 10,
      },
      total: "",
      addUserForm: {
        avatar: null,
        username: "",
        email: "",
        password: "",
        role: "",
        permission_group_id: "",
        phone: "",
        introduction: "",
      },
      ruleInline: {
        username: [{ required: true, message: "用户名", trigger: "blur" }],
        email: [{ required: true, message: "邮箱地址", trigger: "blur" }],
        password: [{ required: true, message: "密码", trigger: "blur" }],
        role: [{ required: true, message: "角色", trigger: "blur" }],
        permission_group_id: [
          { required: true, message: "权限组名称", trigger: "blur" },
        ],
        phone: [{ required: true, message: "手机号", trigger: "blur" }],
      },
      permissionsList: [], //权限列表
      loading: false,
      id: "",
    };
  },

  components: { SearchHeader, PageTool, AddUser },

  created() {
    this.getUserList();
  },
  computed: {
    text() {
      return this.id == "" ? "新增" : " 编辑";
    },
    pageTitle() {
      return this.id == "" ? "用户" : " 用户资料";
    },
  },

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
    indexMethod(index) {
      return (this.queryInfo.page - 1) * this.queryInfo.pagesize + index + 1;
    },
    // 获取用户信息列表
    async getUserList() {
      this.loading = true;
      const res = await list(this.queryInfo);
      this.loading = false;
      this.userData = res.data.list;
      this.queryInfo.page = res.data.page;
      this.queryInfo.pagesize = res.data.pagesize;
      this.total = res.data.counts;
    },
    // 进入某一页页码改变触发
    onPageChange(pageNum) {
      this.queryInfo.page = pageNum;
      this.getUserList();
    },
    // 每页显示信息条数发生改变触发
    onPageSizeChange(pageSize) {
      this.queryInfo.pageSize = pageSize;
      this.getUserList();
    },
    // 获取子组件中的搜索框的值
    searchUser(val) {
      this.queryInfo.page = 1;
      this.queryInfo.username = val;
      this.getUserList();
    },
    onClose() {
      this.dialogFormVisible = false;
      this.id = "";
    },
    newDataes() {
      this.getUserList();
    },
    clearData() {
      this.addUserForm = {};
    },
    clearAll() {
      this.queryInfo.username = "";
      this.getUserList();
    },
    async openDialog() {
      // 打开弹层
      this.dialogFormVisible = true;
      // 获取权限列表
      const { data } = await simple();

      this.permissionsList = data;
    },
    async addNew() {
      this.openDialog();
      // 获取传递过来的表单信息，发送添加人员请求
    },
    // 编辑
    async Edit(id) {
      this.addUserForm.id = id;
      const { data } = await detail(this.addUserForm);
      // console.log(data);
      this.id = id;
      this.addUserForm = data;
      // console.log(res);
      // 获取权限列表
      this.openDialog();
    },
    // 删除
    async delUser(info) {
      await this.$confirm("确定删除？");
      console.log(this.userData);
      if (this.userData.length === 1) {
        this.queryInfo.page = this.queryInfo.page - 1;
        // console.log("0000");
        this.getUserList();
      }
      await remove(info);
      this.$message.success("删除用户成功！");
      this.getUserList();
      console.log(this.userData);
      // if (this.userData.length === 0) {
      //   console.log(312313);
      //   this.queryInfo.page = 1;
      //   console.log("0000");
      //   this.getUserList();
      // }
      // this.getUserList();
      // this.$message.fail("删除用户失败！");
    },
  },
};
</script>

<style scoped lang="less">
// 警示框
.el-alert--info.is-light {
  margin-bottom: 20px;
}
footer {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}
</style>
