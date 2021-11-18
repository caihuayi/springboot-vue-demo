<template>
  <el-menu
      default-active="user"
      class="el-menu-vertical-demo"
      style="width: 200px; min-height: calc(100vh - 50px)"
      default-openeds="1"
  >
    <el-sub-menu index="1">
      <template #title>系统管理</template>
      <el-menu-item index="user">用户管理</el-menu-item>
    </el-sub-menu>
  </el-menu>
</template>

<script>
export default {
  name: "Aside"
}
</script>

<style>

</style>