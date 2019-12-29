<template>
  <div class="app-container">
    <el-button type="primary" size="small" @click="onAdd()">添加</el-button>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="id" label="ID"></el-table-column>
      <el-table-column prop="name" label="标签名" width="180"></el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status == 1" type="success">启用</el-tag>
          <el-tag v-if="scope.row.status == 2" type="danger">禁用</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="created_at" label="创建时间"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" type="primary" @click="onEdit(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <br />
    <el-row>
      <el-col :span="24" :offset="16">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          background
          :page-sizes="[5, 10, 20, 40]"
          :page-size="listQuery.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="this.total"
        ></el-pagination>
      </el-col>
    </el-row>
    <el-dialog :title="this.title" :visible.sync="dialogTableVisible">
      <el-form :model="form" :rules="rules" ref="form" label-width="120px" class="demo-ruleForm">
        <el-form-item label="标签名" prop="name" style="width: 500px;">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="是否启用">
          <el-radio :label="1" v-model="form.status">是</el-radio>
          <el-radio :label="2" v-model="form.status">否</el-radio>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('form')" size="small">保存</el-button>
          <el-button @click="resetForm()" size="small">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import request from "@/utils/request";
import { Loading } from "element-ui";

export default {
  data() {
    return {
      form: {
        name: "",
        status: 1
      },
      listQuery: {
        page: 1,
        pageSize: 10
      },
      title: "",
      dialogTableVisible: false,
      tableData: [],
      total: 0,
      rules: {
        name: [{ required: true, message: "请输入标签名称", trigger: "blur" }]
      }
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      request({
        url: "/label/list",
        method: "get",
        params: this.listQuery
      }).then(res => {
        this.tableData = res.data.data;
        this.total = res.data.total;
      });
    },
    resetForm() {
      this.form = {
        name: "",
        status: 1
      };
    },
    handleSizeChange(val) {
      this.listQuery.pageSize = val;
      this.getList();
    },
    handleCurrentChange(val) {
      this.listQuery.page = val;
      this.getList();
    },
    handleDelete(row) {
      request({
        url: "/label/del",
        method: "post",
        data: { id: row }
      }).then(res => {
        if (res.code === 200) {
          this.getList();
          this.$message({
            message: "删除成功",
            type: "success",
            duration: 1000
          });
        } else {
          this.$message({
            message: res.msg,
            type: "error"
          });
        }
      });
      
    },
    onAdd() {
      this.resetForm();
      this.title = "添加";
      this.dialogTableVisible = true;
    },
    onEdit(row) {
      this.resetForm();
      this.title = "编辑";
      this.form.name = row.name;
      this.form.status = row.status;
      this.dialogTableVisible = true;
    },
    submitForm(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          request({
            url: "/label/save",
            method: "post",
            data: this.form
          }).then(res => {
            if (res.code === 200) {
              this.$message({
                message: "保存成功",
                type: "success",
                duration: 1000
              });
              this.dialogTableVisible = false;
              this.getList();
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
.line {
  text-align: center;
}
</style>

