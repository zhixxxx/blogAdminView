<template>
  <div class="app-container">
    <el-form :inline="true" :model="form" class="demo-form-inline" size="small">
      <el-form-item label="审批人">
        <el-input v-model="form.user" placeholder="审批人"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSearch">查询</el-button>
      </el-form-item>
      <el-form-item>
        <router-link to="/article/add"><el-button type="primary">添加</el-button></router-link>
        
      </el-form-item>
    </el-form>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="content" label="内容"></el-table-column>
      <el-table-column prop="created_at" label="创建时间"></el-table-column>
    </el-table>
  </div>
</template>

<script>

import request from '@/utils/request';

export default {
  data() {
    return {
      form:{
          page:1,
          pageSize:10,
      },
      tableData: []
    };
  },
  mounted() {
      this.getList();
  },
  methods: {
      getList(){
          request({
            url:'/article/list',
            method:'get',
            params: this.form
          }).then(res => {
            this.tableData = res.data.data
          })
      },
    onSubmit() {
      this.getList();
    }
  }
};
</script>

<style scoped>
</style>

