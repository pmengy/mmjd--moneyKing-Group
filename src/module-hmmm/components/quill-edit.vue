<template>
  <div class="example">
    <quill-editor
      class="editor"
      :value="value"
      ref="QuillEditor"
      @change="onEditorChange"
      :options="editorOption"
    />
    <!-- <div class="output code">
      <code class="hljs" v-html="contentCode"></code>
    </div> -->
    <!-- <div class="output ql-snow">
      <div class="ql-editor" v-html="content"></div>
    </div> -->
  </div>
</template>

<script>
import { quillEditor } from "vue-quill-editor";

// highlight.js style
import "highlight.js/styles/tomorrow.css";

// import theme style
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
const toolbarOptions = [
  ["bold", "italic", "underline", "strike"], // 加粗，斜体，下划线，删除线
  [{ list: "ordered" }, { list: "bullet" }], // 有序列表，无序列表
  ["blockquote"], //引用，代码块
  ["code-block", "image", "link"], // 上传图片、上传视频
];

export default {
  name: "quill-example-snow",
  title: "Theme: snow",
  components: {
    quillEditor,
  },
  data() {
    return {
      editorOption: {
        placeholder: "",
        theme: "snow", //主题 snow/bubble
        modules: {
          history: {
            delay: 1000,
            maxStack: 50,
            userOnly: false,
          },
          toolbar: {
            container: toolbarOptions,
            handlers: {
              insertMetric: this.showHandle,
            },
          },
        },
      },
    };
  },
  props: {
    value: {
      type: String,
      default: "",
    },
  },
  mounted() {},
  methods: {
    onEditorChange(e) {
      this.$emit("onEditorChange", e.html);
    },
  },
};
</script>

<style lang="scss" scoped>
.example {
  display: flex;
  flex-direction: column;
  margin-bottom: 30px;
  height: 350px;
  // border-bottom: 1px solid #ccc;

  .editor {
    height: 350px;
    overflow: hidden;
    padding-bottom: 100px;
    border-bottom: unset;
    .qi-container {
      font-size: 16px !important;
    }
  }

  .output {
    width: 100%;
    height: 300px;
    margin: 0;
    border: 1px solid #ccc;
    overflow-y: auto;
    resize: vertical;

    &.code {
      padding: 1rem;
      height: 16rem;
    }

    &.ql-snow {
      border-top: none;
      height: 24rem;
    }
  }
}
</style>
