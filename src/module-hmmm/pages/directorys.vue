<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type="primary" class="bgcolor">
        <i class="el-icon-plus marginRight"></i>新增目录
      </el-button>
      <el-card style="margin-top:20px">
        <el-form inline> 
          <el-form-item label="目录名称">
          <el-input class="marginRight" v-model="fromData.directoryName"></el-input>
          </el-form-item>
          <el-form-item label="状态">
            <el-select class="marginRight" placeholder="请选择" v-model="fromData.state">
              <el-option v-for="item in status" :key="item.value" :value="item.value" :label="item.label"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button class="marginLeft" @click="clid">清除</el-button>
            <el-button class="bgcolor" style="color:#fff">搜索</el-button>
          </el-form-item>
        </el-form>
      </el-card>
      <el-card style="margin-top:20px;padding:0px">
        <el-table :data="list.items">
          <el-table-column prop="id" class="center" label="序号"></el-table-column>
          <el-table-column prop="directoryName" class="center" label="目录名称"></el-table-column>
          <el-table-column prop="username" class="center" label="创建者"></el-table-column>
          <el-table-column prop="addDate" class="center" label="创建日期">
            <template slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</template>
          </el-table-column>
          <el-table-column prop="totals" class="center" align="center" label="面试题数量"></el-table-column>
          <el-table-column prop="state" class="center" align="center" label="状态">
            <template slot-scope="obj">{{obj.row.state===1?"开启":"关闭"}}</template>
          </el-table-column>
          <el-table-column prop="" class="center" label="操作">
            <template slot-scope="obj">
              <el-button type="text">修改</el-button>
            <el-button type="text">{{obj.row.state===1?"禁用":"开启"}}</el-button>
            <el-button type="text">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-row type="flex" justify="end" style="margin:30px 0px">
          <el-pagination background layout="prev, pager, next" :total="100"></el-pagination>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list } from "../../api/hmmm/directorys"
import { status } from "../../api/hmmm/constants"
export default {
  name: 'DirectorysList',
  data() {
    return {
      fromData:{
        directoryName:"",
        state:null
      },
      list:[],
      status
    }
  },
  methods:{
    clid(){
      this.fromData={
        directoryName:"",
        status:null
      }
    },
    gain(){
      list().then(res=>{
        this.list=res.data
      })
    }
  },
  created(){
    this.gain()
  }
}
</script>

<style scoped>
.bgcolor{
  background: linear-gradient(to right, #1493fa, #01c6fa)
}
.marginRight{
  margin-right: 10px;
}
.marginLeft{
  margin-left: 10px;
}
.center{
  text-align: center
}
</style>
