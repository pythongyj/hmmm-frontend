<template>
  <div class="dashboard-container">
    <div class="app-container">
       <el-card class="upCard">
      <el-row slot="header">
      <el-button type="primary">新增试题</el-button>
      <el-button type="primary">批量导入</el-button>
      </el-row>
      <el-form  label-position='left' label-width="70px" :inline="true" size="mini">
              <el-form-item label="学科">
            <el-select  style='width:60%' v-model="searchForm.subjectID" placeholder="请选择">
              <el-option v-for="item in subjectOptions" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
              <el-form-item  label="状态" >
            <el-select  style='width:60%' v-model="searchForm.chkType" placeholder="请选择">
              <el-option v-for="item in chkType" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="难度">
            <el-select  style='width:60%' v-model="searchForm.difficulty" placeholder="请选择">
              <el-option v-for="item in difficulty" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="试题类型">
            <el-select  style='width:60%' v-model="searchForm.questionType" placeholder="请选择">
              <el-option v-for="item in questionType" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="标签">
            <el-input style='width:60%' v-model="searchForm.tags" placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="录入人">
            <el-input  style='width:60%' v-model="searchForm.creatorID" placeholder="请输入"></el-input>
          </el-form-item>
            <el-form-item label="关键字">
            <el-input style='width:60%' v-model="searchForm.keyword" placeholder="请输入题目编号/题干"></el-input>
          </el-form-item>
          <el-form-item label="题目备注">
            <el-input style='width:60%' v-model="searchForm.remarks" placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="二级目录">
            <el-input v-model="searchForm.twoLevelDirectory" style='width:60%' placeholder="请输入二级目录"></el-input>
          </el-form-item>
          <el-form-item label="城市">
            <el-select  v-model="searchForm.provinces" style='width:60%' placeholder="请选择">
              <el-option v-for="(item,index) in provinces" :key="index" :label="item" :value="index"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="区域">
            <el-select  v-model="searchForm.citys" style='width:60%' placeholder="请选择">
              <el-option v-for="(item,index) in citys" :key="index" :label="item" :value="index" >

              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业简称">
            <el-input   v-model="searchForm.shortName" style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="方向">
            <el-input  v-model="searchForm.direction" style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button @click='clear'>清除</el-button>
            <el-button @click='search' type="primary">搜索</el-button>
          </el-form-item>
      </el-form>
    </el-card>
    <el-card class="downCard">
      <el-tabs v-model="activeName" type="card" >
        <el-tab-pane label="全部" name="first"></el-tab-pane>
        <el-tab-pane label="待审核" name="second"></el-tab-pane>
        <el-tab-pane label="已审核" name="third"></el-tab-pane>
      </el-tabs>
      <el-table style="width: 100%" :data="currentList">
      <el-table-column prop='id' label="序号" >
      </el-table-column>
      <el-table-column prop='number' label="试题编号" >
      </el-table-column>
      <el-table-column :formatter='subject' prop='subjectID' label="学科">
      </el-table-column>
      <el-table-column :formatter='queType' prop='questionType' label="题型">
      </el-table-column>
      <el-table-column prop='question' label="题干">
      </el-table-column>
      <el-table-column  prop='addDate' label="录入时间">
        <template slot-scope="obj">
          {{
          obj.row.addDate | parseTimeByString
          }}
        </template>
      </el-table-column>
      <el-table-column prop='creatorID' label="录入人">
      </el-table-column>
      <el-table-column :formatter='hard' prop='difficulty' label="难度">
      </el-table-column>
      <el-table-column :formatter='checkState' prop='chkState' label="审核状态">
      </el-table-column>
      <el-table-column prop='chkRemarks' label="审核意见">
      </el-table-column>
      <el-table-column prop='chkUser' label="审核人">
      </el-table-column>
      <el-table-column :formatter='pubState' prop='publishState'  label="发布状态">
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="obj">
        <el-button size='small' type="text">预览</el-button>
        <el-button @click='upOrDown(obj.row.id)' size='small' type="text">下架</el-button>
        <el-button @click='modify(obj.row.id)' size='small' type="text">修改</el-button>
        <el-button @click='delItem(obj.row.id)' size='small' type="text">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-row type="flex" justify="center" align="middle">
      <el-pagination
  :current-page='page.currentPage' :page-size='page.pageSize'
  background
  layout="prev, pager, next"
  :total="page.total">
</el-pagination>
    </el-row>
    </el-card>
    </div>
   
  </div>
</template>

<script>
import { choice,choicePublish,remove } from '../../api/hmmm/questions'
import { simple } from '../../api/hmmm/subjects'
import { provinces,citys } from '../../api/hmmm/citys'
import { parseTimeByString  } from '../../filters/index'
import { status,difficulty,questionType,direction,chkType,publishType } from '../../api/hmmm/constants'
export default {
  name: 'QuestionsChoice',
  data() {
    return {
      value:'',
      page:{
        currentPage:1,
        pageSize:10,
        total:0
      },
      activeName:'first',
      searchForm:{
        subjectID:'',
        status:'',
        difficulty:'',
        questionType:'',
        direction:'',
        chkType:'',
        tags:'',
        shortName:'',
        creatorID:'',
        provinces:'',
        citys:'',
        remarks:'',
        keyword:'',
        twoLevelDirectory:'',
        publishType:'',
      },
      status,
      difficulty,
      questionType,
      direction,
      chkType,
      publishType,
      list:[],
      subjectOptions:[],
      provinces:[],
      citys:[]
    }
  },
  computed: {
    currentList() {
      if (this.activeName === "first") return this.list.map(item => item);
      else if (this.activeName === "second")
        return this.list.filter(
          item => item.publishState === 0 || item.publishState === 2
        );
      else return this.list.filter(item => item.publishState === 1);
    }
  },

  methods:{
    //修改
    modify(id) {
      this.$router.push({ path: "/questions/new", query: { id } });
    },
    search(){
      this.page.currentPage =1
      this.getcondition()
    },
    //清除
    clear(){
      this.searchForm={
        subjectID:'',
        status:'',
        difficulty:'',
        questionType:'',
        direction:'',
        chkType:'',
        tags:'',
        shortName:'',
        creatorID:'',
        provinces:'',
        citys:'',
        remarks:'',
        keyword:'',
        twoLevelDirectory:'',
        publishType:'',
      }
    },
    subject(row, column, cellValue, index){
       let res= this.subjectOptions.filter(item=>{
         return item.value ==cellValue
       })
       return  res.length ? res[0].label : '未知'
    },
    //删除该条
    delItem(id){
      this.$confirm('Are you sure to delete this item?').then(res=>{
        remove({ id })
      this.$message({ type: "success", message: "删除成功" });
      this.getcondition()
      })
    },
    //是否下架
    upOrDown(id){
      this.$confirm('Are you sure to upOrDown?').then(res=>{
        choicePublish({ id })    //这里的参数 data 为一个对象
      })
      this.getcondition()
    },
    checkState(row, column, cellValue, index){
      if (cellValue == 0) {
        return '待审核'
      } else if(cellValue == 1) {
        return '通过'
      } else if(cellValue == 2){
        return '拒绝'
      } else{
        return '未知'
      }
    },
    hard(row, column, cellValue, index){
      if (cellValue == 1) {
        return '简单'
      } else if(cellValue == 2) {
        return '一般'
      } else if(cellValue == 3){
        return '困难'
      } else{
        return '未知'
      }
    },
    queType(row, column, cellValue, index){
      if ( cellValue == 1) {
        return '单选'
      } else if( cellValue == 2) {
        return '多选'
      } else if( cellValue == 3){
        return '简答'
      } else{
        return '未知'
      }
    },
    // time(row, column, cellValue, index){
    //   return 
      
    // },
    pubState(row, column, cellValue, index){
      if (cellValue == 0) {
        return '待发布'
      } else if(cellValue == 1) {
        return '已发布'
      } else if(cellValue == 2){
        return '已下架'
      } else{
        return '未知'
      }
    },
    getcondition(){
      let params ={
        page:this.page.currentPage,
        pagesize:this.page.pageSize,
        ...this.searchForm
      }
      this.getQuestionList(params)
    },
    getQuestionList(data){
      choice(data).then(res=>{
        // console.log(res.data.items);
        this.list=res.data.items
        this.page.total=res.data.counts
      })
    },
    getSubjectOptions(){
      simple().then(res=>{
        // console.log(res);
        this.subjectOptions=res.data
      })
    },
    getProvinces(){
      this.provinces=provinces()
    },
    getArea(){
      this.citys=citys(this.searchForm.provinces)
    }
  },
  watch:{
    deep:true,
  },
  filters:{
    filterStatus:function(value){
      switch (value) {
      case 0:
        return '启用'
      case 1:
        return '禁用'
      default:
        break
    }
    }
  },
  created(){
    this.getQuestionList()
    this.getSubjectOptions()
    this.getProvinces()
    this.getArea()
  },
}
</script>

<style scoped lang="scss">
// 1. 如果在此处 scoped 当前组件下生效样式
// 2. 在style中的所有选择器 编译的时候会自动带上属性选择器
// 3. .box{} ===> .box[data-v-2c9827a4]{} 交集选择器
// 4. 在当前组件下暴露的标签都会加上 data-v-2c9827a4 属性
// 5. 但是如果是组件，其他组件内部的标签是不会加上这个属性 意味写组件内部的样式是不会生效的
// 6. 其他组件的样式都不会生效
.upCard{
  margin-bottom: 20px;
}
</style>
