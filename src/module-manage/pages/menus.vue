<template>
  <div class="app-container">
    <el-card>
      <!-- 头部搜索框 -->
      <search-header
        :isShowSearch="false"
        right-text="添加菜单"
        @addNew="addNewMenu"
      />
      <!-- 树形表格 -->
      <tree-table
        :data="treeList"
        :columns="columns"
        :defaultExpandAll="true"
        :treeStructure="true"
        @handleUpdate="handleUpdate"
        @removeUser="removeUser"
        :listLoading="loading"
      />
    </el-card>
    <!-- 新建权限弹出层 -->
    <menu-add
      ref="treeTable"
      text="创建"
      :pageTitle="activeName"
      @handleCloseModal="handleCloseModal"
      @changeName="changeName"
    />
  </div>
</template>

<script>
import TreeTable from "../components/TreeTable";
import { list, remove } from "@/api/base/menus";
import SearchHeader from "../components/search-header.vue";
import MenuAdd from "../components/menu-add.vue";
// import { remove } from "../../api/base/menus";
export default {
  data() {
    return {
      name: "treeTable",
      treeList: [],
      columns: [
        { prop: "title", text: "标题", value: "title" },
        { prop: "code", text: "权限组", value: "code" },
      ],
      loading: false,
      activeName: "菜单",
    };
  },
  components: {
    TreeTable,
    SearchHeader,
    MenuAdd,
  },

  created() {
    this.getTreeList();
  },

  methods: {
    async getTreeList() {
      this.loading = true;
      const { data } = await list();
      this.treeList = data;
      this.loading = false;
    },
    // 添加菜单
    addNewMenu() {
      // 打开创建菜单弹层
      this.$refs.treeTable.dialogFormV();
      // 触发添加菜单组件的重置方法
      this.$refs.treeTable.handleResetForm();
    },
    // 取消关闭
    handleCloseModal() {
      // 关闭创建菜单弹层
      this.$refs.treeTable.dialogFormH();
      // 触发添加菜单组件的重置方法
      this.$refs.treeTable.handleResetForm();
      // 获取最新数据重新渲染页面
      this.getTreeList();
    },
    // 点击每一行的编辑图标触发
    handleUpdate({ id }) {
      this.$refs.treeTable.hanldeEditForm(id);
      // 打开创建菜单弹层
      this.$refs.treeTable.dialogFormV();
    },
    // 点击每一行的删除图标触发
    async removeUser(id) {
      // 确认框
      await this.$confirm("确定删除？");
      this.loading = true;
      // 删除权限节点
      await remove(id);
      this.loading = false;
      // 获取最新数据重新渲染页面
      this.getTreeList();
      this.$message.success("删除权限节点成功！");
    },
    changeName(status) {
      // 改变弹出框左上名字
      if (status) {
        return (this.activeName = "权限点");
      }
      this.activeName = "菜单";
    },
  },
};
</script>

<style scoped lang="less"></style>
