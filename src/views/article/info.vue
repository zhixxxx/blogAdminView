<template>
  <div class="app-container">
    <el-form :model="form" label-width="120px" class="demo-ruleForm">
      <el-form-item label="文章标题：" style="width: 400px">{{ form.title }}</el-form-item>
      <el-form-item label="文章描述：" style="width: 500px;">{{ form.desc }}</el-form-item>
      <el-form-item label="文章标签：" prop="label">
        <el-select v-model="form.label" disabled multiple placeholder="请选择">
          <el-option v-for="item in labelList" :key="item.id" :label="item.name" :value="item.id"></el-option>
        </el-select>
      </el-form-item>
      <!-- <el-form-item label="文章标签" prop="category_id">{{ form.category.name }}</el-form-item> -->
      <el-form-item label="图片张数：">
        <template v-if="form.is_single == 0">无图</template>
        <template v-else-if="form.is_single == 1">单图</template>
        <template v-else-if="form.is_single == 2">多图</template>
      </el-form-item>
      <el-form-item label="是否置顶：">
        <template v-if="form.is_top == 1">是</template>
        <template v-if="form.is_top == 2">否</template>
      </el-form-item>
      <el-form-item label="图片：" v-if="form.is_single == 1">
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
      <el-form-item label="列表图片：" v-if="form.is_single == 2">
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
      <el-form-item label="文章内容：">
        <mavon-editor
          class="md"
          :value="form.content"
          :subfield="prop.subfield"
          :defaultOpen="prop.defaultOpen"
          :toolbarsFlag="prop.toolbarsFlag"
          :editable="prop.editable"
          :scrollStyle="prop.scrollStyle"
          :codeStyle="prop.codeStyle"
          :ishljs="prop.ishljs"
        />
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
        id: 9,
        title: "",
        desc: "",
        category_id: "",
        is_single: 0,
        is_top: 2,
        images: "",
        content: ""
      },
      categoryList: [],
      labelList:[],
      rules: {
        title: [{ required: true, message: "请输入活动名称", trigger: "blur" }],
        category_id: [
          { required: true, message: "请选择文章分类", trigger: "blur" }
        ],
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
    this.getLabelList();
    this.getInfo();
  },
  methods: {
    //文章标签列表
    getLabelList() {
      request({
        url: "/label/list",
        method: "get",
        params: { source: "article" }
      }).then(res => {
        this.labelList = res.data;
      });
    },
    getInfo() {
      var id = this.$route.query.id;
      if (id > 0) {
        request({
          url: "/article/info",
          method: "get",
          params: { id: id }
        }).then(res => {
          this.form = res.data;
        });
      }
    },
    submitForm(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          request({
            url: "/article/save",
            method: "post",
            data: this.form
          }).then(res => {
            if (res.code === 200) {
              this.$message({
                message: "保存成功",
                type: "success",
                duration: 1000,
                onClose: () => {
                  this.$router.replace({
                    path: "/article/list"
                  });
                }
              });
            } else {
              this.$message({
                message: res.msg,
                type: "error"
              });
            }
          });
        } else {
          return false;
        }
      });
    }
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

