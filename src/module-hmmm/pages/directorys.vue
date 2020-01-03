<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type="primary" class="bgcolor">
        <i class="el-icon-plus marginRight"></i>新增目录
      </el-button>
      <el-card style="margin-top:20px">
        <span class="marginRight">目录名称</span>
        <el-select placeholder="请选择"  class="marginRight"></el-select>
        <span class="marginRight marginLeft">状态</span>
        <el-select class="marginRight" placeholder="请选择"></el-select>
        <el-button class="marginLeft">清除</el-button>
        <el-button class="bgcolor" style="color:#fff">搜索</el-button>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DirectorysList',
  data() {
    return {}
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
</style>
