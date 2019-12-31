<template>
  <div class="dashboard-container">
    <div class="app-container">
       <el-card class="upCard">
      <div style="margin-bottom:10px;">
      <el-button type="primary">新增试题</el-button>
      <el-button type="primary">批量导入</el-button>
      </div>
      <el-form  label-position='left' label-width="70px" :inline="true" size="mini">
              <el-form-item label="学科">
            <el-select  style='width:60%' v-model="value" placeholder="请选择">
              <el-option>
              </el-option>
            </el-select>
          </el-form-item>
              <el-form-item label="状态">
            <el-select  style='width:60%' v-model="value" placeholder="请选择">
              <el-option>
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="难度">
            <el-select  style='width:60%' v-model="value" placeholder="请选择">
              <el-option>
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="试题类型">
            <el-select  style='width:60%' v-model="value" placeholder="请选择">
              <el-option></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="标签">
            <el-input style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="录入人">
            <el-input  style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
            <el-form-item label="关键字">
            <el-input style='width:60%' placeholder="请输入题目编号/题干"></el-input>
          </el-form-item>
          <el-form-item label="题目备注">
            <el-input style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="二级目录">
            <el-input style='width:60%' placeholder="请输入二级目录"></el-input>
          </el-form-item>
          <el-form-item label="城市">
            <el-select  style='width:60%' placeholder="请选择">
              <el-option ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="区域">
            <el-select  style='width:60%' placeholder="请选择">
              <el-option ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业简称">
            <el-input  style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item label="方向">
            <el-input  style='width:60%' placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button>清除</el-button>
            <el-button type="primary">搜索</el-button>
          </el-form-item>
      </el-form>
    </el-card>
    <el-card class="downCard">
      <el-tabs v-model="activeName" type="card" >
        <el-tab-pane label="全部" name="first"></el-tab-pane>
        <el-tab-pane label="待审核" name="second"></el-tab-pane>
        <el-tab-pane label="已审核" name="third"></el-tab-pane>
      </el-tabs>
      <el-table style="width: 100%" :data="list">
      <el-table-column prop='id' label="序号" >
      </el-table-column>
      <el-table-column prop='number' label="试题编号" >
      </el-table-column>
      <el-table-column prop='subjectID' label="学科">
      </el-table-column>
      <el-table-column prop='questionType' label="题型">
      </el-table-column>
      <el-table-column prop='question' label="题干">
      </el-table-column>
      <el-table-column prop='addDate' label="录入时间">
      </el-table-column>
      <el-table-column prop='creatorID' label="录入人">
      </el-table-column>
      <el-table-column prop='difficulty' label="难度">
      </el-table-column>
      <el-table-column label="使用次数">
      </el-table-column>
      <el-table-column prop='chkState' label="审核状态">
      </el-table-column>
      <el-table-column prop='chkRemarks' label="审核意见">
      </el-table-column>
      <el-table-column prop='chkUser' label="审核人">
      </el-table-column>
      <el-table-column prop='publishState'  label="发布状态">
      </el-table-column>
      <el-table-column label="操作">
        <el-button size='small' type="text">预览</el-button>
        <el-button size='small' type="text">下架</el-button>
        <el-button size='small' type="text">修改</el-button>
        <el-button size='small' type="text">删除</el-button>
      </el-table-column>
    </el-table>
    </el-card>
    </div>
   
  </div>
</template>

<script>
// import {} from 
export default {
  name: 'QuestionsChoice',
  data() {
    return {
      value:'',
      list:[]
    }
  },
  created(){

  }
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
