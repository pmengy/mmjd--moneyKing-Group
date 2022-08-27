<template>
  <el-dialog
    @open="getDetails"
    @close="close"
    title="题目预览"
    :visible="visible"
    width="50%"
  >
    <div class="content">
      <ul class="type">
        <li>【题型】：{{ questionInfo.questionType }}</li>
        <li>【编号】：29</li>
        <li>【难度】：简单</li>
        <li>【标签】：css3 bfc</li>
        <li>【学科】：前端</li>
        <li>【目录】：css基础</li>
        <li>【方向】：企业服务</li>
      </ul>
      <div class="title">
        【题干】：
        <div v-html="questionInfo.question">1+1等于几？</div>
      </div>
      <div class="options">
        <el-radio disabled v-model="radio" label="1">1</el-radio>
        <el-radio disabled v-model="radio" label="2">2</el-radio>
        <el-radio disabled v-model="radio" label="3">3</el-radio>
        <el-radio disabled v-model="radio" label="4">4</el-radio>
      </div>
      <div class="answer">
        【参考答案】：<el-button type="danger" size="small"
          >视频答案预览</el-button
        >
      </div>
      <div class="detail">【答案解析】：无</div>
      <div class="other">【题目备注】：无</div>
    </div>
  </el-dialog>
</template>

<script>
import { detail } from "@/api/hmmm/questions.js";
import randoms from "../constant/randoms";
const questionType = randoms.questionType;
export default {
  name: "ArticlePreview",
  data() {
    return {
      radio: "2",
      id: "",
      questionInfo: {},
      questionType,
    };
  },
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    id: {
      type: Number,
      required: true,
    },
  },
  methods: {
    close() {
      this.$emit("update:visible", false);
    },
    async getDetails() {
      const res = await detail({ id: this.id });
      console.log(res);
      this.questionInfo = res.data;
    },
  },
  // filters: {
  //   formatQuestionType(type) {
  //     const index = this.questionType.findIndex((item) => item.id === type);
  //     return this.questionType[index].value;
  //   },
  // },
};
</script>

<style scoped lang="scss">
.content {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
  .type {
    padding: 0;
    margin: 0;
    list-style: none;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    border-bottom: 2px solid #ccc;
    li {
      width: 25%;
      margin-bottom: 15px;
    }
  }
  .title {
    margin: 10px 0;
    div {
      color: #5252fc;
    }
  }
  .options {
    display: flex;
    flex-direction: column;
    border-bottom: 2px solid #ccc;
    margin-bottom: 10px;
    .el-radio {
      margin: 8px 0;
    }
  }
  .answer {
    padding-bottom: 10px;
    border-bottom: 2px solid #ccc;
  }
  .detail {
    padding: 10px 0;
    border-bottom: 2px solid #ccc;
  }
  .other {
    padding: 10px 0;
    border-bottom: 2px solid #ccc;
  }
}
</style>
