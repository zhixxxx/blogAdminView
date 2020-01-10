<template>
  <div class="app-container">
    <el-form :inline="true" :model="form" class="demo-form-inline" size="small">
      <el-form-item label="标题">
        <el-input v-model="form.title"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSearch()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <router-link to="/article/add">
          <el-button type="primary">添加</el-button>
        </router-link>
      </el-form-item>
    </el-form>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="id" label="ID" width="70"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="desc" label="描述"></el-table-column>
      <el-table-column label="图片数量">
        <template slot-scope="scope">
          <span v-if="scope.row.is_single == 0">无</span>
          <span v-if="scope.row.is_single == 1">1张</span>
          <span v-if="scope.row.is_single == 2">多张</span>
        </template>
      </el-table-column>
      <el-table-column prop="pv" label="访问量"></el-table-column>
      <el-table-column label="是否显示">
        <template slot-scope="scope">
          <el-tag size="medium" v-if="scope.row.status == 1">是</el-tag>
          <el-tag size="medium" v-if="scope.row.status == 2">否</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="是否置顶">
        <template slot-scope="scope">
          <el-tag size="danger" v-if="scope.row.is_top == 1">是</el-tag>
          <el-tag size="danger" v-if="scope.row.is_top == 2">否</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="发文端">
        <template slot-scope="scope">
          <el-tag size="warning" v-if="scope.row.is_admin == 1">后端</el-tag>
          <el-tag size="warning" v-if="scope.row.is_admin == 2">前端</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="创建时间" min-width="85">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span style="margin-left: 10px">{{ scope.row.created_at }}</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="220">
        <template slot-scope="scope">
          <el-button size="mini" type="success" @click="infoRow(scope.row)">详情</el-button>
          <el-button size="mini" type="primary" @click="editRow(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
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
          :page-size="form.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="this.total"
        ></el-pagination>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import request from "@/utils/request";

export default {
  data() {
    return {
      form: {
        page: 1,
        pageSize: 10
      },
      total: 0,
      tableData: []
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      request({
        url: "/article/list",
        method: "get",
        params: this.form
      }).then(res => {
        this.tableData = res.data.data;
        this.total = res.data.total;
      });
    },
    handleSizeChange(val) {
      this.form.pageSize = val;
      this.getList();
    },
    handleCurrentChange(val) {
      this.form.page = val;
      this.getList();
    },
    editRow(row) {
      this.$router.replace({
        path: "/article/add", //跳转文章编译页
        query: {
          id: row.id
        }
      });
    },
    infoRow(row) {
      this.$router.replace({
        path: "/article/info", //跳转文章详情页
        query: {
          id: row.id
        }
      });
    },
    onSubmit() {
      this.getList();
    },
    onSearch() {
      this.getList();
    },

    //删除
    handleDelete(index, row) {
      request({
        url: "/article/del",
        method: "post",
        data: { id: row.id }
      }).then(res => {
        if (res.code === 200) {
          this.$message({
            message: "删除成功",
            type: "success",
            duration: 1000
          });
          this.getList();
        } else {
          this.$message({
            message: res.msg,
            type: "error"
          });
        }
      });
    }
  }
};
</script>

<style scoped>
</style>

