<template>
  <div class="app-container">
    <el-form :model="form" :rules="rules" ref="ruleForm" label-width="120px" class="demo-ruleForm">
      <el-form-item label="文章标题" style="width: 400px" prop="name">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item label="文章描述" style="width: 500px;">
        <el-input type="textarea" v-model="form.desc"></el-input>
      </el-form-item>
      <el-form-item label="文章分类" prop="">
        <el-cascader
          v-model="form.category_id"
          :options="categoryList"
          :props="{ expandTrigger: 'hover' }"
          :show-all-levels="false"
          @change="handleChange"
        ></el-cascader>
        <!-- <el-select v-model="form.category_id" placeholder="请选择活动区域">
      <el-option :label="item.name" :value="item.id" v-for="item in this.categoryList" :key="item.value"></el-option>
        </el-select>-->
      </el-form-item>
      <el-form-item label="图片张数">
        <el-radio :label="0" v-model="form.is_single">无图</el-radio>
        <el-radio :label="1" v-model="form.is_single">单图</el-radio>
        <el-radio :label="2" v-model="form.is_single">多图</el-radio>
      </el-form-item>
      <el-form-item label="是否置顶">
        <el-radio :label="1" v-model="form.is_top">是</el-radio>
        <el-radio :label="2" v-model="form.is_top">否</el-radio>
      </el-form-item>
      <el-form-item label="图片" prop="singleImage" v-if="form.is_single == 1">
        <el-upload
          class="avatar-uploader"
          action="https://jsonplaceholder.typicode.com/posts/"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload"
        >
          <img v-if="form.images" :src="form.images" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="列表图片" prop="manyImage" v-if="form.is_single == 2">
        <el-upload
          action="https://jsonplaceholder.typicode.com/posts/"
          list-type="picture-card"
          :on-preview="handlePictureCardPreview"
          :on-remove="handleRemove"
        >
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="dialogImageUrl" alt />
      </el-dialog>
      <el-form-item label="文章内容" prop="source_content">
        <mavon-editor v-model="form.content" style="min-height: 400px" />
      </el-form-item>
      <el-form-item label="文章内容">
        <mavon-editor
          class="md"
          :value="form.content"
          :subfield="prop.subfield"
          :defaultOpen="prop.defaultOpen"
          :toolbarsFlag="prop.toolbarsFlag"
          :editable="prop.editable"
          :code_style="prop.code_style"
          :scrollStyle="prop.scrollStyle"
        ></mavon-editor>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import request from "@/utils/request";

export default {
  data() {
    return {
      form: {
        name: "",
        desc: "",
        category_id: 0,
        is_single: 0,
        is_top: 2,
        images: "",
        content: ""
      },
      categoryList: [
        {
          label: "第一个",
          value: "1",
          children: [
            { label: "第一个1", value: "1" },
            { label: "第一个2", value: "1" }
          ]
        }
      ],
      rules: {
        name: [{ required: true, message: "请输入活动名称", trigger: "blur" }],
        content: [{ required: true, message: "内容不能为空", trigger: "blur" }]
      },
      dialogImageUrl: "",
      dialogVisible: false
    };
  },
  computed: {
    prop() {
      return {
        subfield: false, // 单双栏模式
        defaultOpen: "preview", //edit： 默认展示编辑区域 ， preview： 默认展示预览区域
        editable: false,
        toolbarsFlag: false,
        scrollStyle: true,
        code_style: "code-github"
      };
    }
  },
  mounted() {
    // this.getCategory();
  },
  methods: {
    handleChange(value) {
      console.log(value);
    },
    //文章分类列表
    getCategory() {
      request({
        url: "/category/list",
        method: "get",
        params: { source: "article" }
      }).then(res => {
        this.categoryList = res.data;
      });
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          console.log(this.form);
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    // 单张图片上传----------------------
    handleAvatarSuccess(res, file) {
      this.form.images = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 2MB!");
      }
      return isJPG && isLt2M;
    },
    // ------------------------------
    // 多张图片上传----------------------
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    }
    // -----------------------------------
  }
};
</script>

<style scoped>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
.blockquote {
  width: 100%;
  height: 300px;
  background: red;
}
</style>

