<template>
  <div class="app-container">
    <el-card class="box-card">
      <el-form :inline="true" class="demo-form-inline">
        <el-row type="flex">
          <el-col>
            <el-form-item label="关键字">
              <el-input
                @keyup.native.enter="onSearch"
                v-model="article.keyword"
                placeholder="根据文章标题搜索"
              ></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-select v-model="article.state" placeholder="请选择">
                <el-option label="启用" :value="1"></el-option>
                <el-option label="禁用" :value="0"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="clearSearch">清除</el-button>
              <el-button type="primary" @click="onSearch">搜索</el-button>
            </el-form-item>
          </el-col>
          <el-row type="flex" justify="end">
            <el-col>
              <el-button @click="addArticle" type="success" icon="el-icon-edit"
                >新增技巧</el-button
              >
            </el-col>
          </el-row>
        </el-row>
      </el-form>
      <alert-tip :total="counts" />
      <!-- 表格 -->
      <el-table fit :data="articleList" style="width: 100%">
        <el-table-column type="index" label="序号"> </el-table-column>
        <el-table-column prop="title" label="文章标题" min-width="300px">
          <template slot-scope="{ row }">
            <p>
              {{ row.title }}
              <i
                @click="showVideo(row.videoURL)"
                style="color: #0000ff; font-size: 18px"
                class="el-icon-film"
                v-if="row.videoURL"
              ></i>
            </p>
          </template>
        </el-table-column>
        <el-table-column prop="visits" label="阅读数"> </el-table-column>
        <el-table-column prop="username" label="录入人"> </el-table-column>
        <el-table-column
          prop="createTime"
          label="录入时间"
          min-width="160px"
          :formatter="formatTime"
        ></el-table-column>
        <el-table-column
          prop="state"
          label="状态"
          :formatter="formatArticleState"
        >
        </el-table-column>
        <el-table-column label="操作" min-width="180px">
          <template slot-scope="{ row }">
            <el-button @click="previewArticle(row)" type="text" size="small"
              >预览</el-button
            >
            <el-button @click="updateState(row)" type="text" size="small">{{
              row.state ? "禁用" : "启用"
            }}</el-button>
            <el-button
              @click="editArticle(row)"
              type="text"
              size="small"
              :disabled="row.state === 1"
              >修改</el-button
            >
            <el-button
              @click="delArticle(row.id)"
              type="text"
              size="small"
              :disabled="row.state === 1"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <div class="pagination">
        <page-pagination
          :total="counts"
          @pageChange="onPageChange"
          @pageSizeChange="onPageSizeChange"
          :paginationPagesize="pagesize"
          :paginationPage="page"
        ></page-pagination>
      </div>
      <!-- 添加/编辑文章弹出层 -->
      <article-add
        :title="title"
        ref="articleAdd"
        :dialogVisible.sync="articleDialog"
        @updateList="getArticleList"
      />
      <el-dialog
        :before-close="onBeforeClose"
        title="播放视频"
        width="50%"
        :visible.sync="videoVisible"
      >
        <div>
          <meta name="referrer" content="no-referrer" />
          <video
            ref="videoPlayer"
            width="700px"
            height="500px"
            controls
            autoplay
          ></video>
        </div>
      </el-dialog>
      <!-- 文章预览 -->
      <el-dialog title="预览文章" width="50%" :visible.sync="visibleArtInfo">
        <div>
          <div>
            <h5 class="title">
              {{ currentRow.title }}
            </h5>
            <p class="description">
              <span>{{ currentRow.createTime | formatDate }}</span>
              <span>{{ currentRow.username }}</span>
              <span>
                <i class="el-icon-view"></i>
                {{ currentRow.visits }}
              </span>
            </p>
          </div>
          <div class="context">
            <article
              class="markdown-body article-content"
              v-highlight
              v-html="currentRow.articleBody"
              style="background-color: #f5f5f5"
            ></article>
          </div>
        </div>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
import dayjs from "dayjs";
import AlertTip from "../components/alert-tip.vue";
import PagePagination from "../components/page-pagination.vue";
import ArticleAdd from "../components/articles-add.vue";
import articleMap from "../constant/article";
import { list, remove, changeState } from "@/api/hmmm/articles.js";
import "../../../node_modules/github-markdown-css/github-markdown.css";
const { articleState } = articleMap;
export default {
  components: { AlertTip, PagePagination, ArticleAdd },
  data() {
    return {
      article: {
        keyword: "",
        state: "",
      },
      articleDialog: false,
      videoVisible: false,
      visibleArtInfo: false,
      articleList: [],
      page: 1,
      pagesize: 10,
      counts: 0,
      articleState,
      title: "新增文章",
      currentRow: {},
    };
  },

  created() {
    this.getArticleList();
  },

  methods: {
    // 获取文章列表
    async getArticleList() {
      if (this.article.state === 0 || this.article.state === 1) {
        try {
          const { data } = await list({
            page: this.page,
            pagesize: this.pagesize,
            keyword: this.article.keyword || null,
            state: this.article.state,
          });
          this.counts = data.counts;
          this.articleList = data.items;
        } catch (error) {
          this.$message.error("网络不太稳定,请刷新重试");
        }
      } else {
        try {
          const { data } = await list({
            page: this.page,
            pagesize: this.pagesize,
            keyword: this.article.keyword || null,
          });
          this.counts = data.counts;
          this.articleList = data.items;
        } catch (error) {
          this.$message.error("网络不太稳定,请刷新重试");
        }
      }
    },
    // 格式化时间和文章状态
    formatTime(row, column, cellValue) {
      return dayjs(cellValue).format("YYYY-MM-DD HH:mm:ss");
    },
    formatArticleState(row, column, cellValue) {
      const findItem = this.articleState.find((item) => item.id === cellValue);
      return findItem ? findItem.value : "未知";
    },
    // 点击页数
    onPageChange(page) {
      this.page = page;
      this.getArticleList();
    },
    // 选择每页条数
    onPageSizeChange(size) {
      this.pagesize = size;
      this.page = 1;
      this.getArticleList();
    },
    // 删除文章
    async delArticle(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          remove({ id });
          this.getArticleList();
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {});
    },
    // 修改文章状态
    async updateState(data) {
      try {
        const index = this.articleList.findIndex((item) => item.id === data.id);
        this.articleList[index].state = this.articleList[index].state ? 0 : 1;
        await changeState({ id: data.id, state: data.state });
        this.getArticleList();
        this.$message.success("操作成功");
      } catch (error) {
        this.$message.error("网络不太稳定,请刷新重试");
      }
    },
    // 清空搜索框
    clearSearch() {
      this.article.keyword = "";
      this.article.state = "";
      this.page = 1;
      this.getArticleList();
    },
    // 搜索
    async onSearch(e) {
      this.page = 1;
      await this.getArticleList();
      if (this.tableData.length <= 0) {
        return this.$message.error("暂无文章");
      }
      e.srcElement.blur();
    },
    // 新增文章
    addArticle() {
      this.title = "新增文章";
      console.log(this.$refs.articleAdd);
      this.$refs.articleAdd.form = {
        title: "",
        articleBody: "",
        videoURL: "",
      };
      this.articleDialog = true;
    },
    // 修改文章
    editArticle(row) {
      this.title = "修改文章";
      this.articleDialog = true;
      this.$refs.articleAdd.form = row;
    },
    // 视频
    showVideo(url) {
      this.videoVisible = true;
      this.$nextTick(() => {
        this.$refs.videoPlayer.src = url;
      });
    },
    previewArticle(content) {
      this.currentRow = content;
      this.visibleArtInfo = true;
    },
    onBeforeClose() {
      this.$refs.videoPlayer.pause();
      this.videoVisible = false;
    },
  },
};
</script>

<style scoped lang="scss">
.box-card {
  .pagination {
    display: flex;
    justify-content: flex-end;
    margin-top: 20px;
  }
}
.title {
  font-size: 21px;
  font-weight: 700;
  color: #606266;
  padding: 0;
  margin: 0;
}
.context {
  background-color: #f5f5f5;
  padding: 20px;
  border-top: 1px dashed #ccc;
}
.description {
  font-size: 14px;
  span {
    margin-right: 10px;
  }
}
</style>
