<template>
  <el-table
    :data="formatData"
    :row-class-name="rowClassStatus"
    v-loading="listLoading"
    element-loading-text="给我一点时间"
    fit
    highlight-current-row
    style="width: 100%"
    default-expand-all
    show-header
    :header-cell-style="{
      background: '#fafafa',
      color: '#9fa2a7',
      borderBottom: '3px solid #e8e8e8',
    }"
  >
    <el-table-column
      v-for="column in columns"
      :key="column.prop"
      :width="column.width"
      :prop="column.prop"
      :label="column.text"
    >
      <template slot-scope="scope">
        <expand
          v-if="column.render"
          :render="column.render"
          :row="scope.row"
          :index="index"
          :column="column"
        >
        </expand>
        <span v-else>
          <template v-if="column.value == 'title'">
            <i
              class="el-icon-folder-opened"
              style="margin-left: 20px"
              v-if="scope.row._level === 0"
            />
            <i
              class="el-icon-document"
              v-if="scope.row._level === 1"
              style="margin-left: 40px"
            />
            <i
              class="el-icon-view"
              v-if="scope.row._level === 2"
              style="margin-left: 60px"
            />
            {{ scope.row[column.value] }}
          </template>
          <template v-if="column.value == 'code'">
            {{ scope.row[column.value] }}
          </template>
        </span>
      </template>
    </el-table-column>
    <el-table-column label="操作" width="260" align="center">
      <template slot-scope="scope">
        <el-button size="mini" type="primary" @click="handleUpdate(scope.row)">
          修改
        </el-button>
        <el-button
          size="mini"
          type="danger"
          @click="handleDelete(scope.row.id)"
        >
          删除
        </el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
import Utils from "./utils/dataTranslate.js";
import expand from "./utils/expand";
export default {
  name: "treeTable",
  components: { expand },
  props: {
    // 该属性是确认父组件传过来的数据是否已经是树形结构了，如果是，则不需要进行树形格式化
    // 判断是否已经处理为对应的树状数据，默认false，表示已经处理过，传true表示没有处理过
    treeStructure: {
      type: Boolean,
      default: function () {
        return false;
      },
    },
    // 需要传递的数组
    data: {
      type: Array,
      default: function () {
        return [];
      },
    },
    // 这是相应的字段展示
    columns: {
      type: Array,
      default: function () {
        return [];
      },
    },
    listLoading: {
      type: Boolean,
      default: false,
    },
    // 是否默认展开所有树
    defaultExpandAll: {
      type: Boolean,
      default: function () {
        return true;
      },
    },
  },
  computed: {
    // 格式化数据源
    formatData: function () {
      const me = this;
      if (me.treeStructure) {
        const data = Utils.treeToArray(
          me.data,
          null,
          null,
          me.defaultExpandAll
        );
        return data;
      }
      return me.data;
    },
  },
  methods: {
    // 行类名状态
    rowClassStatus: function ({ row, rowIndex }) {
      if (row._level === 0) {
        return "levelOne";
      } else if (row._level === 1) {
        return "levelTwo";
      } else if (row._level === 2) {
        return "levelThree";
      } else if (rowIndex === 0) {
        return "headerClass";
      }
    },
    // 修改
    handleUpdate(row) {
      this.$emit("handleUpdate", row);
    },
    // 删除
    handleDelete(user) {
      this.$emit("removeUser", user);
    },
  },
};
</script>
<style rel="stylesheet/css">
.ivu-icon-ios-folder-outline {
  content: "\F434";
}
@keyframes treeTableShow {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@-webkit-keyframes treeTableShow {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>

<style lang="scss" rel="stylesheet/scss" scoped>
$color-blue: #2196f3;
$space-width: 18px;
.ms-tree-space {
  position: relative;
  top: 1px;
  display: inline-block;
  font-style: normal;
  font-weight: 400;
  line-height: 1;
  width: $space-width;
  height: 14px;
  &::before {
    content: "";
  }
}
.processContainer {
  width: 100%;
  height: 100%;
}
table td {
  line-height: 26px;
}

.tree-ctrl {
  position: relative;
  cursor: pointer;
  color: $color-blue;
  margin-left: -$space-width;
}
::v-deep tr.headerClass {
  background-color: red !important;
}
::v-deep.el-table .levelOne {
  background: yellowgreen;
}

::v-deep.el-table .levelTwo {
  background: violet;
  padding-left: 30px;
}
::v-deep.el-table .levelThree {
  background: skyblue;
  margin-left: 60px;
}
</style>
