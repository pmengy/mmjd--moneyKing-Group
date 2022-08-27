<template>
  <div class="app-container">
    <el-card>
      <el-form ref="form" :model="form" label-width="80px">
        <!-- 第一个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-select placeholder="请选择" style="width: 380px">
                  <el-option label="item" value="item"></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>

        <!-- 第2个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-select placeholder="请选择" style="width: 380px">
                  <el-option label="item" value="item"></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>
        <!-- 第3个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-select placeholder="请选择" style="width: 380px">
                  <el-option label="item" value="item"></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>

        <!-- 第4个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="城市" prop="abbreviation">
                <el-select
                  @change="cityChange"
                  placeholder="请选择"
                  style="width: 190px"
                >
                  <el-option
                    v-for="item in citysList"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
                <el-select style="width: 190px">
                  <el-option
                    v-for="item in cityslineLists"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>
        <!-- 第5个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-select placeholder="请选择" style="width: 380px">
                  <el-option label="item" value="item"></el-option>
                </el-select>
              </el-form-item></div
          ></el-col>
        </el-row>
        <!-- 第6个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-radio-group v-model="radio">
                  <el-radio :label="3">备选项</el-radio>
                  <el-radio :label="6">备选项</el-radio>
                  <el-radio :label="9">备选项</el-radio>
                </el-radio-group>
              </el-form-item>
            </div></el-col
          >
        </el-row>
        <!-- 第6个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <el-form-item label="录入人" prop="Entrant">
                <el-radio-group v-model="radio">
                  <el-radio :label="3">备选项</el-radio>
                  <el-radio :label="6">备选项</el-radio>
                  <el-radio :label="9">备选项</el-radio>
                </el-radio-group>
              </el-form-item>
            </div></el-col
          >
        </el-row>
        <!-- 第8个 -->
        <el-row>
          <el-col :span="24"
            ><div class="grid-content bg-purple-dark">
              <!--  新增时输入 -->
              <el-form-item label="录入人" prop="Entrant">
                <quill-editor
                  v-model="content"
                  ref="myQuillEditor"
                  @blur="onEditorBlur($event)"
                  @focus="onEditorFocus($event)"
                  @change="onEditorChange($event)"
                >
                </quill-editor>
              </el-form-item>

              <!-- 从数据库读取展示 -->
              <div class="ql-editor"></div></div
          ></el-col>
        </el-row>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { quillEditor } from "vue-quill-editor";
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import { provinces, citys } from "@/api/hmmm/citys.js";

export default {
  components: {
    quillEditor,
  },
  data() {
    return {
      radio: 3,
      content: `<p></p><p><br></p><ol><li><strong><em>Or drag/paste an image here.</em></strong></li><li><strong><em>rerew</em></strong></li><li><strong><em>rtrete</em></strong></li><li><strong><em>tytrytr</em></strong></li><li><strong><em>uytu</em></strong></li></ol>`,
      str: "",
      editorOption: {},
      citysList: provinces(),
      cityslineLists: [],
    };
  },
  methods: {
    onEditorReady(editor) {
      // 准备编辑器
    },
    onEditorBlur() {}, // 失去焦点事件
    onEditorFocus() {}, // 获得焦点事件
    onEditorChange() {}, // 内容改变事件
    cityChange() {
      this.cityslineLists = citys(val);
    },
    // 转码
    escapeStringHTML(str) {
      str = str.replace(/&lt;/g, "<");
      str = str.replace(/&gt;/g, ">");
      return str;
    },
  },
  computed: {
    editor() {
      return this.$refs.myQuillEditor.quill;
    },
  },
  mounted() {
    let content = ""; // 请求后台返回的内容字符串
    this.str = this.escapeStringHTML(content);
  },
};
</script>

<style scoped lang='less'>

</style>
