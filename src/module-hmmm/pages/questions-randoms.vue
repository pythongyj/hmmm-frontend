<template>
  <el-card>
    <el-table :data="list" style="width: 100%">
      <el-table-column prop="id" label="序号" width="80"></el-table-column>
      <el-table-column prop="addTime" label="组题时间"></el-table-column>
      <el-table-column prop="userName" align="center" label="用户名"></el-table-column>
      <el-table-column prop="questionIDs" label="试题ID"></el-table-column>
      <el-table-column prop="progressOfAnswer" align="center" label="答题进度">
        <span slot-scope="obj">{{obj.row.progressOfAnswer+'%'}}</span>
      </el-table-column>
      <el-table-column prop="accuracyRate" align="center" label="正确率">
        <span slot-scope="obj">{{obj.row.accuracyRate+'%'}}</span>
      </el-table-column>
      <el-table-column prop="totalSeconds" align="center" label="答题总时耗(秒)"></el-table-column>
      <el-table-column prop="questionType" label="组题类型/详情"></el-table-column>
      <el-table-column prop="address" align="center" label="操作">
        <el-button slot-scope="obj" @click="delItem(obj.row.id)" type="text" size="small">删除</el-button>
      </el-table-column>
    </el-table>
    <el-row type="flex" justify="end" style="height:60px" align="middle">
      <el-pagination 
      background 
      :page-size="page.pagesize"
      :current-page="page.currentPage"
      @current-change="changePage"
      layout="prev, pager, next" 
      :total="page.total">
      </el-pagination>
    </el-row>
  </el-card>
</template>

<script>
import { randoms,removeRandoms } from "../../api/hmmm/questions";

export default {
  name: "QuestionsRandoms",
  data() {
    return {
      list: [],
      page:{
        currentPage:1,
        pageSize:10,
        total:0
      }
    };
  },
  methods: {
    async delItem(id){
      await this.$confirm("是否删除此数据");
      await removeRandoms({id});
      this.$message({type:"success",message:"删除成功"});
      this.getCondition();
    },
    changePage(newPage){
      this.page.currentPage = newPage;
      this.getCondition()
    },
    getCondition(){
      let params = {
        page: this.page.currentPage,
        pageSize: this.page.pageSize
      }
      this.getRandoms(params);
    },
    async getRandoms(data) {
      let res = await randoms(data);
      this.list = res.data.items;
      this.page.total = res.data.counts;
    }
  },
  created() {
    this.getRandoms();
  }
};
</script>

<style scoped>
</style>