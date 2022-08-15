<template>
  <div class="add-form">
    <el-dialog
      :title="text + pageTitle"
      :visible="dialogFormVisible"
      @close="onClose"
      v-loading="loading"
    >
      <el-form
        :rules="ruleInline"
        ref="dataForm"
        :model="formBase"
        label-position="left"
        label-width="120px"
        style="width: 400px; margin-left: 120px"
      >
        <el-form-item :label="$t('table.username')" prop="username">
          <el-input v-model="formBase.username"></el-input>
        </el-form-item>
        <el-form-item :label="$t('table.email')" prop="email">
          <el-input v-model="formBase.email"></el-input>
        </el-form-item>
        <el-form-item
          :label="$t('table.paddword')"
          prop="password"
          v-if="id == ''"
        >
          <el-input v-model="formBase.password"></el-input>
        </el-form-item>

        <!-- 角色 -->
        <el-form-item :label="$t('table.role')" prop="role">
          <el-input v-model="formBase.role"></el-input>
        </el-form-item>
        <!-- 权限组 -->
        <el-form-item
          :label="$t('table.permissionUser')"
          prop="permission_group_id"
        >
          <el-select class="filter-item" v-model="formBase.permission_group_id">
            <el-option
              v-for="item in PermissionGroupsList"
              :value="item.id"
              :key="item.key"
              :label="item.title"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item :label="$t('table.phone')" prop="phone">
          <el-input v-model="formBase.phone"></el-input>
        </el-form-item>

        <!-- 头像上传下一个版本再做 -->
        <!-- <el-form-item :label="$t('table.avatar')" prop="avatar">
            <el-upload
              class="upload-demo"
              :action="importFileUrl"
              :on-change="handleChange"
              :file-list="fileList" accept="image/jpeg,image/gif,image/png,image/bmp"
              :before-upload="beforeAvatarUpload">
              <el-button size="small" type="primary">点击上传</el-button>
              <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
        </el-form-item>-->
        <el-form-item :label="$t('table.introduction')">
          <el-input
            type="textarea"
            :autosize="{ minRows: 2, maxRows: 4 }"
            placeholder="Please input"
            v-model="formBase.introduction"
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleClose">{{ $t("table.cancel") }}</el-button>
        <el-button type="primary" @click="createData">{{
          $t("table.confirm")
        }}</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { detail, update, add } from "@/api/base/users";
export default {
  name: "usersAdd",
  props: [
    "text",
    "pageTitle",
    "PermissionGroupsList",
    "formBase",
    "ruleInline",
    "dialogFormVisible",
    "id",
  ],
  data() {
    return {
      loading: false,
      // dialogFormVisible: true,
      // fileList: [],
      // importFileUrl: 'https://jsonplaceholder.typicode.com/posts/',
    };
  },

  // async created() {
  //   const res = await detail(this.formBase.id);
  //   console.log(res);
  // getDetail(this.formBase.id);
  // },
  watch: {},
  computed: {
    uploadObj() {
      let {
        avatar,
        email,
        id,
        introduction,
        permission_group_id,
        phone,
        role,
        username,
      } = this.formBase;
      const obj = {
        avatar,
        email,
        id,
        introduction,
        permission_group_id,
        phone,
        role,
        username,
      };
      return obj;
    },
  },
  methods: {
    // 弹层显示
    // dialogFormV() {
    //   this.dialogFormVisible = true;
    // },
    // 弹层隐藏
    // dialogFormH() {
    //   this.dialogFormVisible = false;
    // },
    // 退出
    handleClose() {
      this.$emit("closeDialog");
    },
    // 弹层关闭
    onClose() {
      // this.id = "";
      this.$emit("closeDialog");
      this.$refs.dataForm.resetFields();
      // this.formBase.id ? (this.formBase.id = null) : "";
      this.$emit("clearData");
      // 移除所有表单值和校验结果
      this.formBase.introduction = "";
    },
    // 表单提交
    createData() {
      this.$refs.dataForm.validate(async (valid) => {
        if (valid) {
          // 编辑
          this.loading = true;
          if (this.id) {
            //编辑
            try {
              await update(this.uploadObj);
              this.$emit("newDataes", this.uploadObj);
              this.loading = false;
              this.$message.success("更新用户信息成功！");
              this.onClose();
            } catch (error) {
              this.$message.fail("请稍后重试");
            }
          } else {
            //新增
            this.formBase.sex = 1;
            this.formBase.avatar = "";
            try {
              await add(this.formBase);
              this.$message.success("添加用户成功！");
              this.loading = false;
              this.onClose();
              this.$emit("newDataes", this.formBase);
            } catch (error) {
              this.$message.fail("添加用户失败！");
            }
          }
        } else {
          this.$Message.error("*号为必填项!");
        }
      });
    },
    // 获取用户信息
    // async getDetail(id) {
    //   const res = await detail(id);
    //   console.log(res);
    // },
  },
  // 挂载结束

  mounted: function () {},
  // 创建完毕状态
  created() {},
  // 组件更新
  updated: function () {},
};
</script>
<style>
.el-form--label-left .el-form-item__label {
  text-align: right;
}
.el-form-item--medium {
  margin-bottom: 22px;
}
.el-dialog__footer {
  text-align: center;
}
</style>
