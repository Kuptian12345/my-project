<template>
  <el-container class="container">
    <el-header class="header">
      <el-col :span="4">
        <img src="@/assets/logo.png" alt="图片加载失败">
      </el-col>
      <el-col :span="19" class="font">
        <h2>品优购管理系统</h2>
      </el-col>
      <el-col :span="1">
        <a href="#" class="loginout">退出</a>
      </el-col>
    </el-header>
    <el-container>
      <el-aside class="aside" width="200px">Aside</el-aside>
      <el-main class="main">Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {};
</script>

<style>
.container {
  height: 100%;
}
.header {
  background-color: #b3c0d1;
}
.aside {
  background-color: yellow;
}
.main {
  background-color: orange;
}
.font {
  line-height: 60px;
  text-align: center;
  color: #fff;
}
.loginout {
  text-decoration: none;
  line-height: 60px;
  text-align: center;
}
</style>
