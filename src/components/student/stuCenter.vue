<template>
  <!-- 个人信息 -->
  <el-card>
    <el-descriptions :column="2" :model="stuDate">
      <el-descriptions-item label="姓名">
        {{ stuDate.StudentName }}
      </el-descriptions-item>
      <el-descriptions-item label="年龄">
        {{ stuDate.StudentAge }}
      </el-descriptions-item>
      <el-descriptions-item label="性别">
        {{ stuDate.StudentSex }}
      </el-descriptions-item>
      <el-descriptions-item label="学号">
        {{ stuDate.SNumber }}
      </el-descriptions-item>
      <el-descriptions-item label="专业班级">
        {{ stuDate.SFormClass }}
      </el-descriptions-item>
      <el-descriptions-item label="所属学院">
        {{ stuDate.CollegeName }}
      </el-descriptions-item>
      <el-descriptions-item label="邮箱">
        {{ stuDate.stuemail }}
      </el-descriptions-item>
    </el-descriptions>
    <el-row
      ><el-button @click="dialogFormVisible = true">修改密码</el-button></el-row
    >
    <!-- 修改密码弹窗 -->
    <div>
      <!-- :visible.sync控制弹窗显示隐藏 -->
      <el-dialog :visible.sync="dialogFormVisible" overflow="auto">
        <el-form :rules="FormRules" ref="Form" :model="Form">
          <el-form-item label="修改密码" prop="password1">
            <el-input type="password" v-model="Form.password1"></el-input>
          </el-form-item>
          <el-form-item label="再次输入" prop="password2">
            <el-input type="password" v-model="Form.password2"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer">
          <el-row>
            <el-button type="primary" @click="onsubmit()"
              >确 定</el-button
            ></el-row
          >
        </div>
      </el-dialog>
    </div>
  </el-card>
</template>


<script>
export default {
  data() {
    return {
      // 修改密碼
      Form: {
        password1: "",
        password2: "",
      },
      // 个人信息
      stuDate: {
        StudentName: "",
        StudentAge: "",
        StudentSex: "",
        SNumber: "",
        CollegeName: "",
        SFormClass: "",
        stuemail: "",
      },

      // 弹窗显示隐藏
      dialogTableVisible: false,
      dialogFormVisible: false,
      screenWidth: null, //屏幕尺寸

      // 输入框验证
      FormRules: {
        password1: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 2, max: 11, message: "2~11位", trigger: "blur" },
        ],

        password2: [
          { required: true, message: "请再次输入", trigger: "blur" },
          { min: 2, max: 11, message: "2~11位", trigger: "blur" },
        ],
      },
    };
  },
  created: function () {
    this.stumessage();
  },
  methods: {
    // 信息展示
    async stumessage() {
      let token = localStorage.getItem("token");
      let email = localStorage.getItem("account");
      // var email = '322@qq.com'
      const { data: res } = await this.$http.get(
        `/api/student/personal?token=` + token + "&email=" + email
      );
      // console.log(res);
      // console.log(res.data);
      if (res.code == 200) {
        this.stuDate.StudentAge = res.data[0].StudentAge;
        this.stuDate.StudentName = res.data[0].StudentName;
        this.stuDate.StudentSex = res.data[0].StudentSex;
        this.stuDate.SNumber = res.data[0].SNumber;
        this.stuDate.CollegeName = res.data[0].CollegeName;
        this.stuDate.SFormClass = res.data[0].SFormClass;
        this.stuDate.stuemail = res.data[0].email;
      } else if (res.code == 100) {
        this.$message.error("当前登录非学生账号");
      }
      // console.log(res.data);
      // 渲染信息
      // this.stuDate.SFormClass = res.data[0].SFormClass;
    },

    // 弹窗里确定修改密码
    async onsubmit() {
      let token = localStorage.getItem("token"); //取token
      let account = localStorage.getItem("account");
      var password1 = this.Form.password1;
      var password2 = this.Form.password2;
      if (password1 == "" || password2 == "") {
        this.$message.error("请输入密码");
        return false;
      }
      //  两次密码输入一致
      if (password1 !== password2) {
        //  console.log(password1);
        //  console.log(password2);
        this.$message.error("两次输入不一致");
        return false;
      } else {
        const { data: res } = await this.$http.post(
          `/api/teacher/teachermodifycode?token=` + token,
          {
            account: account,
            password: password1,
          }
        );
        // console.log(res.data);
        if (res.code == 200) {
          this.dialogFormVisible = false;
          this.$message.success("修改成功！");
          this.Form.password1 = "";
          this.Form.password2 = "";
        } else {
          this.message.error("修改失败");
        }
      }
    },
  },
};
</script>
<style scoped>
.el-row {
  text-align: center;
  /* margin: 20px; */
}
.el-dialog {
  text-align: center;
}
.el-form {
  width: 560px;
}
.el-descriptions {
  height: 250px;
  font-size: 20px;
}
.el-input {
  width: 30vw;
}
@media screen and (max-width: 670px) {
  .el-descriptions {
    font-size: 10px;
  }
  .el-input {
    width: 19vw;
  }
}
</style>
