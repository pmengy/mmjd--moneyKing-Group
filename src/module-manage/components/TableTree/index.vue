<template>
  <div>
    <el-table
      :data="PermissionsList"
      style="width: 100%; margin-bottom: 20px"
      row-key="id"
      border
      default-expand-all
      :tree-props="{
        children: 'childs' || 'points',
        hasChildren: 'points',
      }"
    >
      <el-table-column prop="title" label="标题" sortable width="180">
      </el-table-column>
      <el-table-column prop="name" label="权限代码" sortable width="180">
      </el-table-column>
      <el-table-column prop="code" label="操作"> </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { list } from "@/api/base/menus";
export default {
  name: "MmjdMoneyKingGroupIndex",

  data() {
    return {
      PermissionsList: [], //存放权限列表
      tableData: [
        {
          id: 1,
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄",
        },
        {
          id: 2,
          date: "2016-05-04",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1517 弄",
        },
        {
          id: 3,
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄",
          children: [
            {
              id: 31,
              date: "2016-05-01",
              name: "王小虎",
              address: "上海市普陀区金沙江路 1519 弄",
            },
            {
              id: 32,
              date: "2016-05-01",
              name: "王小虎",
              address: "上海市普陀区金沙江路 1519 弄",
            },
          ],
        },
        {
          id: 4,
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1516 弄",
        },
      ],
      tableData1: [
        {
          id: 1,
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄",
        },
        {
          id: 2,
          date: "2016-05-04",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1517 弄",
        },
        {
          id: 3,
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄",
          hasChildren: true,
        },
        {
          id: 4,
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1516 弄",
        },
      ],
    };
  },

  mounted() {},
  created() {
    this.grtPermissionsList();
  },

  methods: {
    async grtPermissionsList() {
      const { data } = await list();
      this.PermissionsList = data;
    },
  },
};
</script>

<style lang="scss" scoped></style>
