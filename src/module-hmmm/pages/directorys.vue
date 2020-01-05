<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type="primary" class="bgcolor" @click="addmulu">
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
            <el-button class="bgcolor" @click="search" style="color:#fff">搜索</el-button>
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
              <el-button type="text" @click="modification(obj.row.id)">修改</el-button>
            <el-button type="text" @click="switchs(obj.row)" >{{obj.row.state===1?"禁用":"开启"}}</el-button>
            <el-button type="text" @click="delItem(obj.row.id)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-row type="flex" justify="end" style="margin:30px 0px">
          <el-pagination background layout="prev, pager, next"
           :total="page.total"
           :current-page="page.currentPage"
           :page-size="page.pageSize"
          ></el-pagination>
        </el-row>
      </el-card>
      <el-dialog :visible="dialogVisiblie" :show-close="false" :title="title">
        <el-form :model="addXiu" :rules="rules" ref="addxiu">
            <el-form-item label="目录名称" prop="directoryName">
              <el-input v-model="addXiu.directoryName" style="width:50%"></el-input>
            </el-form-item>
            <el-form-item label="学科" prop="subjectID">
              <el-select v-model="addXiu.subjectID">
                <el-option v-for="item in subject" :key="item.value" :value="item.value" :label="item.label"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        <el-row slot="footer">
          <el-button type="primary" class="bgcolor" @click="truee(addXiu)">确定</el-button>
          <el-button @click="cancel">取消</el-button>
        </el-row>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import { list , remove , removeState ,add ,detail,update } from "../../api/hmmm/directorys"
import { status } from "../../api/hmmm/constants"
import { simple } from "../../api/hmmm/subjects"
import { log } from 'util'
import { type } from 'os'

export default {
  name: 'DirectorysList',
  data() {
    return {
      rules:{
        directoryName:[{required:true,message:"请输入目录名称",trigger:"blur"}],
        subjectID:[{required:true,message:"学科不能为空",trigger:"blur"}]
      },
      fromData:{
        directoryName:"",
        state:null
      },
      list:[],
      status,
      page:{
        currentPage:1,
        pageSize:10,
        total:0
      },
      dialogVisiblie:false,
      addXiu:{
        directoryName:"",
        subjectID:null
      },
      subject:[],
      title:"添加目录"
    }
  },
  methods:{
    async modification(id){
      this.title="修改目录"
      this.dialogVisiblie=true
      let res=await detail({ id })
      this.addXiu=res.data
    },
    addmulu(){
      this.title="添加目录"
      this.dialogVisiblie=true
    },
    cancel(){
      this.addXiu={
        directoryName:"",
        subjectID:null
      }
      this.dialogVisiblie=false
    },
    async truee(addXiu){
      await this.$refs.addxiu.validate()
      let res= await this.addXiu.id? update(this.addXiu) : add(this.addXiu)
      this.$message({
        message:"操作成功",
        type:"success"
      })
      this.addXiu={
        directoryName:"",
        subjectID:null
      }
      this.search()
      this.dialogVisiblie=false
    },
    async xueke(){
      let res=await simple()
      this.subject=res.data
      console.log(res.data);
    },
    async switchs(row){
      await this.$confirm("您确定要修改此状态吗？")
      await removeState({id:row.id,state:row.state===1?0:1})
      this.$message({message:"状态修改成功",type:"success"})
      this.search()
    },
    async delItem(id){
     await this.$confirm("您确定要删除此目录吗？")
     await remove({ id })
     this.$message({  message:"删除成功",type:"success" })
     this.search()
    },
    search(){
      this.page.currentPage=1,
      this.gain(this.fromData)
    },
    clid(){
      this.gain()
      this.fromData={
        directoryName:"",
        status:null
      }
    },
    async gain(data){
      let res=await list({
        page:this.page.currentPage,
        pagesize:this.page.pageSize,
        ...data
      })
      this.list=res.data
      this.page.total=res.data.counts
    }
  },
  created(){
    this.xueke()
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
