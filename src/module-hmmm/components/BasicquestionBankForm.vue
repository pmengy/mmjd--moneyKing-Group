<template>
 
    <el-card>
      <!-- 头部 -->
      <el-row style="margin-bottom: 15px">
        <el-col :span="12"
          ><div class="grid-content bg-purple">
            <p class="text-illustrate">说明：目前支持学科和关键字条件筛选</p>
          </div></el-col
        >
        <el-col :span="12"
          ><div class="grid-content bg-purple">
            <el-button style="margin-left: 450px" type="success"
              >成功按钮</el-button
            >
          </div></el-col
        >
      </el-row>
      <!-- 表单区域 -->
      <el-form ref="form" :model="form" label-width="80px">
        <!-- 第一层 -->
        <el-row :gutter="20">
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="学科">
                <el-select
                  placeholder="请选择"
                  v-model="form.discipline"
                  @change="onChange"
                >
                  <el-option
                    v-for="(item, index) in disciplineLists"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="二级目录">
                <el-select placeholder="请选择" v-model="form.Secondary">
                  <el-option
                    v-for="item in disciplineList"
                    :label="item.directoryName"
                    :value="item.subjectID"
                    :key="item.id"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="标签">
                <el-select placeholder="请选择" v-model="form.tags">
                  <el-option
                    v-for="item in labelList"
                    :key="item.id"
                    :label="item.tagName"
                    :value="item.tagName"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="关键字">
                <el-input
                  placeholder="根据题干搜索"
                  v-model="form.keywords"
                ></el-input>
              </el-form-item></div
          ></el-col>
        </el-row>
        <!-- 第二层 -->
        <el-row :gutter="20">
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="试题类型">
                <el-select placeholder="请选择" v-model="form.type">
                  <el-option
                    v-for="item in questionType"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="难度">
                <el-select placeholder="请选择" v-model="form.difficulty">
                  <el-option
                    v-for="item in questionType"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="方向">
                <el-select placeholder="请选择" v-model="form.direction">
                  <el-option
                    v-for="item in direction"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="录入人">
                <el-select placeholder="请选择" v-model="form.Entrant">
                  <el-option
                    v-for="item in administrator"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>
        <!-- 第三层 -->
        <el-row :gutter="20">
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="题目备注">
                <el-input v-model="form.titleNotes"></el-input>
              </el-form-item></div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-form-item label="企业简称">
                <el-input v-model="form.abbreviation"></el-input>
              </el-form-item></div
          ></el-col>
          <el-col :span="6">
            <div class="grid-content bg-purple">
              <el-form-item label="试题类型">
                <el-select
                  @change="cityChange"
                  v-model="form.city"
                  placeholder="请选择"
                  style="width: 90px"
                >
                  <el-option
                    v-for="item in citysList"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
                <el-select style="width: 90px">
                  <el-option
                    v-for="item in disciplineLists"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </div>
          </el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple">
              <el-button size="small " style="margin-left: 120px"
                >清除</el-button
              >
              <el-button size="small " type="primary">搜索</el-button>
            </div></el-col
          >
        </el-row>
      </el-form>
    </el-card>
</template>

<script>
import { list } from "@/api/hmmm/tags.js";
import { simple } from "@/api/hmmm/subjects.js";
import { lists } from "@/api/hmmm/directorys.js";
import { questionType, difficulty, direction } from "@/api/hmmm/constants.js";
import { provinces, citys } from "@/api/hmmm/citys.js";
export default {
  data() {
    return {
      disciplineList: [],
      form: {
        discipline: "",
        Secondary: "",
        tags: "",
        keywords: "",
        type: "",
        difficulty: "",
        direction: "",
        Entrant: "",
        titleNotes: "",
        abbreviation: "",
        city: "",
      },
      labelList: [],
      districtList: [],
      citysList: provinces(),
      disciplineLists: [],
      questionType: [],
      difficulty: [],
      direction: [],
      administrator: ["超级管理员", "录入管理员"],
    };
  },
  created() {
    this.getlist()
    this.questionType = questionType;
    this.difficulty = difficulty;
    this.direction = direction;
  },
  methods: {
    async getlist() {
      const res = await simple();
      this.disciplineLists = res.data;
    },
    // 二级目录
    async lists(id) {
      const ree = await lists({
        subjectID: id,
      });
      this.disciplineList = ree.data.items;
    },
    // 标签
    async labelLists(id) {
      const rew = await list({
        subjectID: id,
      });
      console.log(rew);
      this.labelList = rew.data.item;
    },
    async onChange(id) {
      await (this.form.Secondary = ""), (this.form.tags = "");
      this.lists(id);
      this.labelLists(id);
    },
    cityChange(val) {
      this.disciplineLists = citys(val);
    },
  },
};
</script>

<style scoped lang="less">
.text-illustrate {
  font-size: 12px;
  color: pink;
}
</style>
