<template>
  <el-container class="article_list">
    <el-main class="main">
      <blog_table state="1" :showEdit="true" :showDelete="true" :activeName="activeName"></blog_table>
    </el-main>
  </el-container>
</template>
<script>
  import BlogTable from '@/components/BlogTable'
  export default {
    mounted: function () {},
    data() {
      return {
        activeName: 'post',
        isAdmin: true
      };
    },
    methods: {},
    components: {
      'blog_table': BlogTable
    }
  };
</script>
<style>
  .article_list > .header {
    background-color: #ececec;
    margin-top: 10px;
    padding-left: 5px;
    display: flex;
    justify-content: flex-start;
  }

  .article_list > .main {
    justify-content: flex-start;
    display: flex;
    flex-direction: column;
    padding-left: 0px;
    background-color: #fff;
    padding-top: 0px;
    margin-top: 8px;
  }
</style>
