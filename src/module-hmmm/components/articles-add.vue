<template>
  <el-dialog
    @close="onClose"
    :title="title"
    :visible="dialogVisible"
    width="60%"
  >
    <el-form ref="form" :model="form" :rules="formRules" label-width="100px">
      <el-form-item label="文章标题：" prop="title">
        <el-input v-model="form.title" placeholder="请输入文章标题"></el-input>
      </el-form-item>
      <el-form-item label="文章内容：" prop="articleBody">
        <quill-editor
          @onEditorChange="onEditorChange"
          :value="form.articleBody"
        />
      </el-form-item>
      <el-form-item label="视频地址：">
        <el-input
          v-model="form.videoURL"
          placeholder="请输入视频地址"
        ></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="onClose">取 消</el-button>
      <el-button type="primary" @click="onSave">确 定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import QuillEditor from "../components/quill-edit.vue";
import { add, update } from "@/api/hmmm/articles";
export default {
  components: { QuillEditor },
  data() {
    return {
      form: {
        title: "",
        articleBody: "",
        videoURL: "",
      },
      formRules: {
        title: [
          {
            required: true,
            message: "请输入文章标题",
            trigger: "blur",
          },
        ],
        articleBody: [
          { required: true, message: "请输入文章内容", trigger: "blur" },
        ],
      },
    };
  },
  props: {
    title: {
      type: String,
      required: true,
    },
    dialogVisible: {
      type: Boolean,
      required: true,
    },
  },
  methods: {
    async onSave() {
      try {
        await this.$refs.form.validate();
        if (this.form.id) {
          await add(this.form);
          this.$emit("updateList");
          this.$message.success("新增文章成功");
        } else {
          await update(this.form);
          this.$emit("updateList");
          this.$message.success("修改文章成功");
        }
      } catch (error) {
        return this.$message.error("网络不太稳定,请稍后重试");
      } finally {
        this.dialogVisible = false;
      }
    },
    onEditorChange(val) {
      this.form.articleBody = val;
    },
    onClose() {
      this.$emit("update:dialogVisible", false);
    },
  },
};
</script>

<style scoped lang="scss"></style>
