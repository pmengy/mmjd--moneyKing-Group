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
      >
      </tree-table>
    </el-card>
    <!-- 新建权限弹出层 -->
    <menu-add />
  </div>
</template>

<script>
import TreeTable from "../components/TreeTable";
import { list } from "@/api/base/menus";
import SearchHeader from "../components/search-header.vue";
import MenuAdd from "../components/menu-add.vue";
export default {
  data() {
    return {
      name: "treeTable",
      treeList: [],
      columns: [
        { prop: "title", text: "标题", value: "title", width: "200px" },
        { prop: "code", text: "权限组", value: "code", width: "800px" },
      ],
    };
  },
  components: {
    TreeTable,
    SearchHeader,
  },

  created() {
    this.getTreeList();
  },

  methods: {
    async getTreeList() {
      const { data } = await list();
      this.treeList = data;
    },
    // 添加菜单
    addNewMenu() {
      console.log(555);
    },
  },
};
</script>

<style scoped lang="less"></style>
