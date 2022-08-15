<template>
  <div class="app-container">
    <div>菜单管理</div>
    <tree-table :data="treeList" />
  </div>
</template>

<script>
import { list } from "@/api/base/menus";
import TreeTable from "@/components/TreeTable";
export default {
  data() {
    return {
      queryInfo: {
        page: 1,
        pagesize: 10,
      },
      treeList: [],
    };
  },
  components: {
    TreeTable,
  },

  created() {
    this.getPermissionList();
  },

  methods: {
    async getPermissionList() {
      const { data } = await list();
      // console.log(res);
      this.treeList = data;
    },
  },
};
</script>

<style scoped lang="less"></style>
