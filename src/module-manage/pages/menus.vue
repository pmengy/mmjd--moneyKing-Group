<template>
  <div class="app-container">
    <el-card>
      <tree-table
        :data="treeList"
        :columns="columns"
        :defaultExpandAll="true"
        :treeStructure="true"
      />
    </el-card>
  </div>
</template>

<script>
import TreeTable from "../components/TreeTable";
import { list } from "@/api/base/menus";
export default {
  data() {
    return {
      name: "treeTable",
      treeList: [],
      columns: [
        { prop: "title", text: "标题", value: "code" },
        { prop: "code", text: "权限组", value: "code" },
      ],
    };
  },
  components: {
    TreeTable,
  },

  created() {
    this.getTreeList();
  },

  methods: {
    async getTreeList() {
      const { data } = await list();
      this.treeList = data;
    },
  },
};
</script>

<style scoped lang="less"></style>
