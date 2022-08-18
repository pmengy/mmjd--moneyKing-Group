<template>
  <div class="app-container">
    <el-card class="box-card">
      <div class="form">
        <el-form ref="form" :model="data" label-width="80px">
          <div class="oneline">
            <el-form-item label="标签名称">
              <el-input v-model="data.tags" placeholder="请输入"></el-input>
            </el-form-item>
            <el-form-item label="城市">
              <el-select
                v-model="data.city"
                placeholder="请选择"
                @change="getCity(data.city)"
              >
                <el-option
                  v-for="(item, index) in provincesList"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="地区">
              <el-select v-model="data.province" placeholder="请选择">
                <el-option
                  v-for="(item, index) in cityList"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="企业简称">
              <el-input
                v-model="data.shortName"
                placeholder="请输入"
              ></el-input>
            </el-form-item>
          </div>
          <div class="twoline">
            <el-form-item label="状态" style="flex: 1">
              <el-select v-model="data.state" placeholder="请选择">
                <el-option
                  v-for="(item, index) in status"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <div class="btn" style="flex: 1">
              <el-button @click="onMove">清除</el-button>
              <el-button type="primary" @click="search">搜索</el-button>
            </div>

            <el-form-item style="flex: 2">
              <el-button
                type="success"
                class="el-icon-edit addBtn"
                @click="comAdd"
                >新增用户</el-button
              >
            </el-form-item>
          </div>
        </el-form>
      </div>
      <div class="title">
        <span class="el-icon-info"></span>
        <p>
          共 <span>{{ total }}</span> 条记录
        </p>
      </div>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column align="center" prop="id" label="序号">
        </el-table-column>
        <el-table-column align="center" prop="number" label="企业编号">
        </el-table-column>
        <el-table-column align="center" prop="shortName" label="企业简称">
        </el-table-column>
        <el-table-column align="center" prop="tags" label="标签">
        </el-table-column>
        <el-table-column align="center" prop="creatorID" label="创建者">
        </el-table-column>
        <el-table-column align="center" prop="addDate" label="创建日期">
        </el-table-column>
        <el-table-column align="center" prop="remarks" label="备注">
        </el-table-column>
        <el-table-column
          align="center"
          prop="state"
          label="状态"
          :formatter="formatter"
        >
        </el-table-column>
        <el-table-column align="center" label="操作" width="150px">
          <template slot-scope="scope">
            <el-button
              class="el-icon-edit"
              size="mini"
              circle
              type="primary"
              plain
              @click="handleEdit(scope.row)"
            ></el-button>

            <el-tooltip
              class="item"
              effect="dark"
              content="屏蔽"
              placement="top"
            >
              <el-button
                v-show="scope.row.state == 1"
                class="el-icon-close"
                type="warning"
                size="mini"
                circle
                plain
                @click="handleChange(scope.row)"
              ></el-button>
            </el-tooltip>
            <el-tooltip
              class="item"
              effect="dark"
              content="开启"
              placement="top"
            >
              <el-button
                v-show="scope.row.state == 0"
                type="success"
                icon="el-icon-check"
                size="mini"
                circle
                plain
                @click="handleChange(scope.row)"
              ></el-button>
            </el-tooltip>
            <el-button
              class="el-icon-delete"
              size="mini"
              circle
              type="danger"
              plain
              @click="handleDelete(scope.row)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[10, 20, 30, 50]"
        :page-size="10"
        layout=" prev, pager, next,sizes, jumper"
        :total="this.total"
        class="footpage"
      >
      </el-pagination>
      <companysAdd
        ref="add"
        :titleInfo="titleInfo"
        :formBase="formBase"
        @newDataes="newDataes"
      ></companysAdd>
    </el-card>
  </div>
</template>

<script>
import companysAdd from "../components/companys-add.vue";
import { list, remove, disabled } from "@/api/hmmm/companys";
import { provinces, citys } from "@/api/hmmm/citys.js";
import { status } from "@/api/hmmm/constants";
export default {
  name: "company",
  data() {
    return {
      total: 0,
      tableData: [{}],
      data: {
        page: 1,
        pagesize: 10,
      },
      currentPage: 1,
      status: status,
      provincesList: [], //城市列表
      cityList: [], //地区列表
      formBase: {},
      titleInfo: {
        text: "",
        pageTitle: "用户",
      },
    };
  },
  components: { companysAdd },
  created() {
    this.getList();
    this.getProvinces();
  },

  methods: {
    async getList() {
      const res = await list(this.data);
      this.tableData = res.data.items;
      this.total = res.data.counts;
      console.log(res, "企业列表");
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.data.pagesize = val;
      this.getList();
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.data.page = val;
      this.getList();
    },
    //获取城市列表
    getProvinces() {
      this.provincesList = provinces();
      // console.log(this.provincesList);
    },
    //根据城市获取地区列表
    getCity(val) {
      this.cityList = citys(val);
      // console.log(this.cityList);
    },
    async search() {
      const res = await list(this.data);
      console.log(res, 123123);
      this.tableData = res.data.items;
      this.total = res.data.counts;
    },
    onMove() {
      this.data = {};
      this.cityList = {};
    },
    //新建用户
    comAdd() {
      this.titleInfo.text = "新增";
      this.formBase = {};
      this.$refs.add.dialogFormV();
    },
    //编辑
    handleEdit(row) {
      this.titleInfo.text = "编辑";
      this.formBase = row;
      this.$refs.add.dialogFormV();
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
    async newDataes(data) {
      // if (data.id) {
      //   await update(data);
      // } else {
      //   await add(data);
      // }
      this.getList();
    },
    formatter(row, column, cellValue) {
      if (row.state == 1) {
        return (cellValue = "开启");
      }
      return (cellValue = "屏蔽");
    },
    //设置状态
    async handleChange(row) {
      const data = {
        id: row.id,
        state: "",
      };
      if (row.state == 0) {
        this.$confirm("已屏蔽, 是否开启?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        })
          .then(async () => {
            data.state = 1;
            await disabled(data);
            this.$message.success("开启成功");
            this.getList();
            this.$message({
              type: "success",
              message: "开启成功!",
            });
          })
          .catch(() => {
            this.$message({
              type: "info",
              message: "已取消开启",
            });
          });
      } else {
        this.$confirm("已开启, 是否屏蔽?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        })
          .then(async () => {
            data.state = 0;
            await disabled(data);
            this.$message.success("屏蔽成功");
            this.getList();
            this.$message({
              type: "success",
              message: "屏蔽成功!",
            });
          })
          .catch(() => {
            this.$message({
              type: "info",
              message: "已取消屏蔽",
            });
          });
      }
    },
  },
};
</script>

<style scoped lang="less">
.form {
  .oneline {
    display: flex;
    justify-content: space-around;
  }
  .twoline {
    position: relative;
    display: flex;
    justify-content: space-between;
    .btn {
      padding-left: 40px;
    }
    .addBtn {
      position: absolute;
      right: 0;
      top: 0;
    }
  }
}
.title {
  display: flex;
  align-items: center;
  width: 100%;
  height: 35px;
  margin: 0 0 20px;
  padding: 8px 16px;
  background-color: #f4f4f5;
  color: #909399;
  p {
    padding: 0 8px;
    font-size: 13px;
  }
}
.footpage {
  display: flex;
  justify-content: flex-end;
}
</style>
