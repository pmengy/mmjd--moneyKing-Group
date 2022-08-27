<template>
  <div class="app-container">
    <el-card class="box-card">
      <div class="search-container">
        <div class="search-field">
          <span>关键字</span
          ><el-input
            @keyup.native.enter="onSearch"
            v-model="keyword"
            placeholder="根据编号搜索"
          ></el-input>
        </div>
        <div>
          <el-button @click="clearSearch">清除</el-button>
          <el-button type="primary" @click="onSearch">搜索</el-button>
        </div>
      </div>
      <alert-tip :total="total" />
      <!-- 表格 -->
      <el-table v-loading="loading" fit :data="tableData" style="width: 100%">
        <el-table-column min-width="250px" prop="id" label="编号">
        </el-table-column>
        <el-table-column
          prop="questionType"
          label="题型"
          :formatter="formatQuestionType"
        >
        </el-table-column>
        <el-table-column min-width="250px" label="题目编号">
          <template slot-scope="{ row }">
            <div v-for="item in row.questionIDs" :key="item.number">
              <a @click="showDetail(item.id)" style="color: #0099ff" href="#">{{
                item.number
              }}</a>
            </div>
          </template>
        </el-table-column>
        <el-table-column
          min-width="200px"
          prop="addTime"
          label="录入时间"
        ></el-table-column>
        <el-table-column
          prop="totalSeconds"
          label="答题时间(s)"
        ></el-table-column>
        <el-table-column
          prop="accuracyRate"
          label="正确率(%)"
        ></el-table-column>
        <el-table-column prop="userName" label="录入人"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="{ row }">
            <el-button
              @click="onRemove($event, row)"
              plain
              type="danger"
              icon="el-icon-delete"
              circle
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="pagination">
        <page-pagination
          @pageChange="onPageChange"
          @pageSizeChange="onPageSizeChange"
          :total="total"
          :paginationPagesize="paginationPagesize"
          :paginationPage="paginationPage"
        ></page-pagination>
      </div>
      <ArticlePreview :id="currentId" :visible.sync="visiblePreview" />
    </el-card>
  </div>
</template>

<script>
import AlertTip from "../components/alert-tip.vue";
import PagePagination from "../components/page-pagination.vue";
import ArticlePreview from "../components/articles-preview.vue";
import { randoms, removeRandoms } from "@/api/hmmm/questions.js";
import randomsMap from "../constant/randoms";
const { questionType } = randomsMap;
export default {
  name: "QuestionsRandoms",
  components: { AlertTip, PagePagination, ArticlePreview },
  data() {
    return {
      keyword: "",
      tableData: [],
      total: 0,
      paginationPagesize: 20,
      paginationPage: 1,
      loading: false,
      visiblePreview: false,
      questionType,
      currentId: "",
    };
  },
  created() {
    this.getList();
  },
  methods: {
    // 获取题组列表
    async getList() {
      try {
        this.loading = true;
        const { data } = await randoms({
          page: this.paginationPage,
          pagesize: this.paginationPagesize,
          keyword: this.keyword,
        });
        this.tableData = data.items;
        this.total = data.counts;
        this.paginationPagesize = data.pagesize;
        this.paginationPage = data.page;
      } catch (error) {
        this.$message.error("网络不太稳定,请稍后重试");
      } finally {
        this.loading = false;
      }
    },
    // 点击页数
    onPageChange(page) {
      this.paginationPage = page;
      this.getList();
    },
    // 选择每页条数
    onPageSizeChange(size) {
      this.paginationPagesize = size;
      this.paginationPage = 1;
      this.getList();
    },
    // 搜索
    async onSearch(e) {
      this.paginationPage = 1;
      await this.getList();
      if (this.tableData.length <= 0) {
        return this.$message.error("该编号暂无组题");
      }
      e.srcElement.blur();
    },
    // 清空搜索框
    clearSearch() {
      this.keyword = "";
      this.paginationPage = 1;
      this.getList();
    },
    // 点击删除按钮
    onRemove(evt, row) {
      this.$confirm("此操作将永久删除该题组, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          // 点击确定按钮
          await removeRandoms(row);
          this.$message({
            type: "success",
            message: "删除成功!",
          });
          this.getList();
        })
        .catch(() => {})
        .finally(function () {
          // 强制让删除按钮失去焦点
          let target = evt.target;
          if (target.nodeName == "I") {
            target = evt.target.parentNode;
          }
          target.blur();
        });
    },
    // 格式化题组类型
    formatQuestionType(row, column, cellValue) {
      const findItem = this.questionType.find((item) => item.id === cellValue);
      return findItem ? findItem.value : "未知";
    },
    showDetail(id) {
      this.visiblePreview = true;
      this.currentId = id;
      // console.log(id);
    },
  },
};
</script>

<style scoped lang="less">
.search-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20px;
  padding-left: 20px;
  .search-field {
    display: flex;
    align-items: center;
    span {
      width: 60px;
      font-size: 14px;
      font-weight: 700;
      color: #666;
    }
  }
  .el-input__inner {
    width: 200px !important;
  }
}
.box-card {
  .pagination {
    display: flex;
    justify-content: flex-end;
    margin-top: 20px;
  }
}
</style>
