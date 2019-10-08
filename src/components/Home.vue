<template>
  <el-container class="home_container">
    <el-header>
      <div class="home_title" @click="show=!show">达瓦里西</div>
      <div class="home_userinfoContainer">
        <el-dropdown @command="handleCommand">
  <span class="el-dropdown-link home_userinfo">
    {{currentUserName}}<i class="el-icon-arrow-down el-icon--right home_userinfo"></i>
  </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="logout" divided>退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </el-header>
    <el-container>
      <el-aside :width="show?'0':'150px'" class="menu-content">
        <el-menu
          default-active="0"
          class="el-menu-vertical-demo" style="background-color: white" router>
          <template v-for="(item,index) in this.$router.options.routes" v-if="!item.hidden">
            <el-submenu :index="index+''" v-if="item.children.length>1" :key="index">
              <template slot="title">
                <i :class="item.iconCls"></i>
                <span>{{item.name}}</span>
              </template>
              <el-menu-item v-for="child in item.children" v-if="!child.hidden" :index="child.path" :key="child.path">
                {{child.name}}
              </el-menu-item>
            </el-submenu>
            <template v-else>
              <el-menu-item :index="item.children[0].path">
                <i :class="item.children[0].iconCls"></i>
                <span slot="title">{{item.children[0].name}}</span>
              </el-menu-item>
            </template>
          </template>
        </el-menu>
      </el-aside>
      <el-container>
        <el-main  v-loading="loadings">

          <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item v-text="this.$router.currentRoute.name"></el-breadcrumb-item>
          </el-breadcrumb>

          <keep-alive>
            <router-view v-if="this.$route.meta.keepAlive"></router-view>
          </keep-alive>
          <router-view v-if="!this.$route.meta.keepAlive"></router-view>


          <el-carousel :interval="3000" type="card">
            <el-carousel-item v-for="(image, index) in images" :key="index">
              <el-image
                style="width: 100%; height: 100%"
                :src="image.url"
                :fit="fit"></el-image>
            </el-carousel-item>
          </el-carousel>

          <el-divider><i class="el-icon-mobile-phone"> 留 言 板</i></el-divider>
          <el-input
            type="textarea"
            :autosize="{ minRows: 10, maxRows: 15}"
            placeholder="请输入留言"
            v-model="textarea1"
            style="width: 50%;float: right"
          >
          </el-input>


          <div style="width: 46% ;float: left">
            <div v-for="(message, index) in messages" :key="index">
              <el-divider content-position="right">{{message.areainfo}}</el-divider>
              <p>{{message.content}}</p>
              <h5>{{message.time}}</h5>
            </div>
          </div>

          <el-button v-on:click="leavemsg"  style="position: center" type="primary" icon="el-icon-edit" round size="mini">发送留言</el-button>

        </el-main>
      </el-container>
    </el-container>
  </el-container>
</template>
<script>
  import {getRequest, postRequest2} from '../utils/api'
  import ArticleList from "./ArticleList";
  import DataCharts from "./DataCharts";
  export default{
    components: {DataCharts, ArticleList},
    methods: {
      handleCommand(command){
        var _this = this;
        if (command == 'logout') {
          this.$confirm('注销登录吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(function () {
            getRequest("/logout")
            _this.currentUserName = '游客';
            _this.$router.replace({path: '/'});
          }, function () {
            //取消
          })
        }
      },
      leavemsg(){
        var _this=this;
        _this.loadings = true;
          postRequest2('/leavemsg',this.textarea1).then(function (data) {
            let re=data.data;
            if (re){
              _this.textarea1='';
              _this.loadings = false;
              if(re.flag){
                _this.$message({
                  message: re.msg,
                  type: 'success'
                });
              }else{
                _this.$message.error(re.msg);
              }
            }
          })
      }
    },
    mounted: function () {
      var _this = this;
      getRequest("/currentUserName").then(function (msg) {
        _this.currentUserName = msg.data;
      }, function (msg) {
        _this.currentUserName = '游客';
      });

      getRequest('/getlmsg').then(function (data) {
        let re=data.data;
        if(re.flag){
          _this.messages=JSON.parse(re.msg);
        }
      })

      setInterval(function(){ getRequest('/getlmsg').then(function (data) {
        let re=data.data;
        if(re.flag){
          _this.messages=JSON.parse(re.msg);
        }
      }) },5000)

      this.images=[
        {url:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1566030463849&di=a0497ce2b61bbc4c81028533bb1bbc47&imgtype=0&src=http%3A%2F%2Fpicture.ik123.com%2Fuploads%2Fallimg%2F170111%2F12-1F111160205.jpg'},
        {url:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1566030463852&di=0bcd8a4b66b5fb05a12c1bf3802a832d&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F0%2F56dea0c09f196.jpg'},
        {url:'http://dpic.tiankong.com/tv/cv/QJ6365683511.jpg?x-oss-process=style/show'},
        {url:'http://p2-q.mafengwo.net/s13/M00/0C/19/wKgEaVzgMkSAfFppAANKw2l0M0o56.jpeg?imageView2%2F2%2Fw%2F680%2Fq%2F90%7CimageMogr2%2Fstrip%2Fquality%2F90'},
        {url:'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1566030463849&di=674706d45e06dfdb5ac4926283413e6a&imgtype=0&src=http%3A%2F%2Fpic1.win4000.com%2Fwallpaper%2F2018-05-17%2F5afd67beea28c.jpg'}
      ]
    },
    data(){
      return {
        loadings: false,
        currentUserName: '',
        show:false,
        textarea1: '',
        activeName:1,
        messages:[],
        images:[],
        fit:'scale-down'
      }
    }
  }
</script>
<style>
  .home_container {
    height: 100%;
    position: absolute;
    top: 0px;
    left: 0px;
    width: 100%;
    background-color: white;
  }

  .el-header {
    background-color:#3F51B5;
    color: #333;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .el-aside {
    background-color: white;
  }

  .el-main {
    color: #000;
    text-align: center;
  }

  .home_title {
    color: white;
    font-size: 22px;
    display: inline;
  }

  .home_userinfo {
    color: white;
    cursor: pointer;
  }

  .home_userinfoContainer {
    display: inline;
    margin-right: 20px;
  }

  .menu-content{
    transition: all 0.5s ease;
  }

</style>
