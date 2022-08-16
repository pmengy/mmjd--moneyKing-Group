<template>
  <el-table
    :data="currentList"
    style="width: 100%"
    :header-cell-style="{
      background: '#f3f6fb',
      color: '#666',
      height: '42px',
      'font-weight': 400,
    }"
    :highlight-current-row="true"
    empty-text="暂无数据"
  >
    <el-table-column type="index" label="序号" width="80"> </el-table-column>
    <el-table-column
      v-for="(item, index) in tableLabel"
      :key="index"
      :prop="item.prop"
      :width="item.width"
      :label="item.label"
      :formatter="formatData"
    >
    </el-table-column>
    <el-table-column label="操作" width="220">
      <template slot-scope="scope">
        <el-button
          type="text"
          style="color: #4368e1"
          @click.native="$emit('disable', scope.row.classId)"
          >禁用</el-button
        >
        <el-button
          type="text"
          style="color: #4368e1"
          @click.native="$emit('compile', scope.row)"
          >修改</el-button
        >
        <el-button
          type="text"
          style="color: #4368e1"
          @click.native="$emit('Dev', scope.row.id)"
          >删除</el-button
        >
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
import dayjs from "dayjs";

export default {
  name: "dierct",
  data() {
    return {};
  },
  props: {
    currentList: {
      type: Array,
      required: true,
    },
    tableLabel: {
      type: Array,
      default: () => [],
    },
    currentIndex: {
      type: Number,
      required: true,
    },
  },
  created() {},

  methods: {
    formatData(row, column, cellcValue, index) {
      // console.log(column);
      if (column.label === "状态") {
        return cellcValue === 0 ? "已禁用" : "启用";
      } else if (column.label === "创建日期") {
        return dayjs(cellcValue).format("YYYY-MM-DD HH:mm:ss");
      } else {
        return cellcValue;
      }
    },
  },
};
</script>

<style lang="scss">
.el-table td,
.el-table th.is-leaf {
  border-bottom: unset;
}
.el-table td,
.el-table th {
  padding: 5px 0;
}
.el-table thead {
  font-weight: 400;
  color: #666;
}
.el-image__inner {
  display: block;
  width: 35px;
  height: 35px;
  -o-object-fit: cover;
  object-fit: cover;
  border-radius: 50%;
  background: #f3f6fb;
  border: 1px solid #f3f6fb;
}
</style>
