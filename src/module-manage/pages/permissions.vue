<template>
  <div class="app-container">
    <el-card class="box-card">
      <div class="text item">
        <div class="tool">
          <div class="left">
            <el-input v-model="input" placeholder="根据用户名搜索"></el-input>
            <el-button @click="input = ''">清除</el-button>
            <el-button type="primary" @click="search">搜索</el-button>
          </div>
          <div class="right">
            <el-button type="success" class="el-icon-edit" @click="perAdd"
              >新增权限组</el-button
            >
          </div>
        </div>
        <div class="title">
          <span class="el-icon-info"></span>
          <p>
            共 <span>{{ total }}</span> 条记录
          </p>
        </div>
        <div class="table">
          <el-table
            :header-cell-style="{ background: '#fafafa' }"
            :data="tableData"
            style="width: 100%"
            @selection-change="handleSelectionChange"
            ref="multipleTable"
          >
            <af-table-column
              header-align="center"
              align="center"
              type="selection"
              width="55"
            >
            </af-table-column>
            <af-table-column
              header-align="center"
              align="center"
              prop="title"
              label="用户名"
            >
            </af-table-column>
            <af-table-column
              :formatter="formatData"
              header-align="center"
              align="center"
              prop="create_date"
              label="日期"
              sortable
            >
            </af-table-column>
            <af-table-column
              header-align="center"
              align="center"
              label="操作"
              width="150"
            >
              <template slot-scope="scope">
                <el-button
                  class="el-icon-edit"
                  size="mini"
                  circle
                  type="primary"
                  plain
                  @click="handleEdit(scope.row)"
                ></el-button>
                <el-button
                  class="el-icon-delete"
                  size="mini"
                  circle
                  type="danger"
                  plain
                  @click="handleDelete(scope.row)"
                ></el-button>
              </template>
            </af-table-column>
          </el-table>
          <div class="paging">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="currentPage"
              background
              :page-sizes="[10, 20, 30, 50]"
              :page-size="10"
              layout=" prev, pager, next, sizes, jumper"
              :total="total"
            >
            </el-pagination>
          </div>
        </div>
      </div>
    </el-card>
    <permissionsAdd
      @getList="getList"
      ref="Add"
      :pageTitle="pageTitle"
      :text="text"
    ></permissionsAdd>
  </div>
</template>

<script>
import { list, remove } from "@/api/base/permissions";
import permissionsAdd from "../components/permissions-add.vue";
export default {
  name: "PERMISS",
  data() {
    return {
      input: "",
      tableData: [],
      data: {
        page: "1",
        pagesize: "10",
        title: "",
      },
      currentPage: 1,
      total: 0,
      text: "",
      pageTitle: "权限组",
    };
  },
  components: { permissionsAdd },
  created() {
    this.getList();
  },

  methods: {
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    handleSizeChange(val) {
      this.data.pagesize = val;
      console.log(`每页 ${val} 条`);
      this.getList();
    },
    handleCurrentChange(val) {
      this.data.page = val;
      console.log(`当前页: ${val}`);
      this.getList();
    },
    async getList() {
      const res = await list(this.data);
      this.tableData = res.data.list;
      this.total = res.data.counts;
      console.log(res);
      console.log(this.tableData, "权限数据列表");
    },
    formatData(row, column, cellValue, index) {
      if ((cellValue = row.create_date)) {
        const obj = cellValue.split("T");
        return obj[0];
      }
    },
    //搜索框
    async search() {
      this.data.title = this.input;
      const res = await list(this.data);
      this.tableData = res.data.list;
      this.total = res.data.counts;
      console.log(res, "搜索权限数据列表");
    },
    //新建
    perAdd() {
      this.text = "创建";
      this.$refs.Add.dialogFormV();
    },
    //编辑
    handleEdit(row) {
      this.text = "编辑";
      this.$refs.Add.dialogFormV();
      this.$refs.Add.hanldeEditForm(row.id);
    },
    //删除
    handleDelete(row) {
      this.$confirm("此操作将永久删除, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          const data = { id: row.id };
          await remove(data);
          this.$message.success("删除成功");
          this.getList();
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style scoped lang="less">
.tool {
  display: flex;
  justify-content: space-between;
  .left {
    display: flex;
    font-size: 12px;
    /deep/.el-input__inner {
      width: 200px;
      height: 32px;
      font-size: 13px;
    }
    /deep/.el-button--medium {
      width: 56px;
      height: 32px;
      margin-left: 10px;
      padding: 0 15px;
      font-size: 12px;
    }
  }
  .right {
    /deep/.el-button--medium {
      width: 109px;
      height: 32px;
      padding: 0 15px;
      font-size: 12px;
    }
  }
}
.title {
  display: flex;
  align-items: center;
  width: 100%;
  height: 35px;
  margin: 20px 0 20px;
  padding: 8px 16px;
  background-color: #f4f4f5;
  color: #909399;
  p {
    padding: 0 8px;
    font-size: 13px;
  }
}
.paging {
  display: flex;
  flex-direction: row-reverse;
}
</style>
