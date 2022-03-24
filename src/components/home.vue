<template>
    <div class="container">
        <el-container class="home_container">
            <el-header>
                <div class="header">
                     <img src="https://www.nsu.edu.cn/img/logo.png" alt="">
                </div>
               <el-button type="danger" @click="loginout">退出登录</el-button>
            </el-header>
            <el-container>
                <el-aside  :width="isCollapse?'64px':'250px'">

                  <div class="toggle-button" @click="tooggleCollapse">|||</div>

                    <el-menu
            :collapse-transition="false"
            :collapse="isCollapse"
            background-color="#496EE3"
            text-color="#fff"
            active-text-color="#00FFFF"
            unique-opened
            :router="true">
            <el-submenu index="/stuAchievement">
            <template slot="title">
                <i class="el-icon-user"></i>
                <span>学生</span>
            </template>
            <el-menu-item index="/stuAchievement">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>个人成绩</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/stuCenter">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>个人中心</span>
                </template>
              </el-menu-item>
            </el-submenu>

           <el-submenu index="/majorClass">
              <template slot="title">
                <i class="el-icon-user"></i>
                <span>老师</span>
              </template>
              <el-menu-item index="/majorClass">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>专业班级</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/teaCenter">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>个人中心</span>
                </template>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="/xueyuan">
              <template slot="title">
                <i class="el-icon-user"></i>
                <span>院长</span>
              </template>
              <el-menu-item index="/xueyuan">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>学院</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/dTeacher">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>学院教师</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/dirCenter">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>个人中心</span>
                </template>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="/university">
              <template slot="title">
                <i class="el-icon-s-custom"></i>
                <span>校长</span>
              </template>
               <el-menu-item index="/university">
                <template slot="title">
                  <i class="el-icon-school"></i>
                  <span>学校</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/hTeacher">
                <template slot="title">
                  <i class="el-icon-reading"></i>
                  <span>教师</span>
                </template>
              </el-menu-item>
              <el-menu-item index="/marjorClass2">
                <template slot="title">
                  <i class="el-icon-menu"></i>
                  <span>专业班级</span>
                </template>
              </el-menu-item>
            </el-submenu>

                    </el-menu>
                </el-aside>
                <el-main>
          <router-view></router-view>
        </el-main>
            </el-container>
        </el-container>
    </div>

</template>

<script>
export default {
   data(){
       return{
          isCollapse:true
       }
   },
    methods: {
      tooggleCollapse(){
        this.isCollapse=!this.isCollapse
      },
    loginout() {
      this.$router.push("/login");
    },

  },
}
</script>

<style scoped>
.container {
  height: 100%;
  overflow: scroll;
}

.home-container {
  height: 100%;
}

.el-header {
  background-color:  #1B3AA4;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header {
  display: flex;
  align-items: center;
}

.header img {
  width: 180px;
}

.el-aside {
  background-color: #496EE3;
}

.el-aside .el-menu {
  border-right: none;
}

.el-main {
  padding: 5px;
  background-color: #eaedf1;
}
.el-container.is-vertical {
    height: 100%;
    flex-direction: column;
}
.toggle-button{
  background-color: #4263ce;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>