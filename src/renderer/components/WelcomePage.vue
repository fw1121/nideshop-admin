<template>
  <div class="content-page">
    <div class="content-nav">
      <el-breadcrumb class="breadcrumb" separator="/">
        <el-breadcrumb-item :to="{ path: '/dashboard' }">后台主页</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="content-main">
        欢迎使用骨朵儿海外精选后台管理系统{{version}}
    </div>
  </div>
</template>

<script>

 import api from '@/config/api';
  export default {
    data() {
      return {
        dialogVisible: false,
        version : api.version,
      }
    },
    components: {
      
    },
  }
</script>

<style scoped>

</style>
