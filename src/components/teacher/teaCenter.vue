<template>
  <!-- 教师个人信息 -->
  <el-card>
    <el-descriptions :column="2" :model="teacherDate">
      <el-descriptions-item label="姓名">
        {{ teacherDate.TeacherName }}
      </el-descriptions-item>
      <el-descriptions-item label="年龄">
        {{ teacherDate.TeacherAge }}
      </el-descriptions-item>
      <el-descriptions-item label="性别">
        {{ teacherDate.TeacherSex }}
      </el-descriptions-item>
      <el-descriptions-item label="电话">
        {{ teacherDate.TeacherPhone }}
      </el-descriptions-item>
      <el-descriptions-item label="邮箱">
        {{ teacherDate.TeacherEmail }}
      </el-descriptions-item>
      <el-descriptions-item label="所属学院">
        {{ teacherDate.TFormCollege }}
      </el-descriptions-item>
      <el-descriptions-item label="教授科目">
        {{ teacherDate.SubjectName }}
      </el-descriptions-item>
      <el-descriptions-item label="教师是否院长">
        {{ teacherDate.ifPresident }}
      </el-descriptions-item>
      <el-descriptions-item label="教师职称">
        {{ teacherDate.cheng }}
      </el-descriptions-item>
    </el-descriptions>

    <!-- 教授班级平均分 -->
    <el-table :data="teachtableData" align="center" style="width: 100%">
      <el-table-column
        prop="ClassName"
        label="教授教授班级"
        width="150"
      ></el-table-column>
      <el-table-column prop="grade" label="期末平均分" width="100">
      </el-table-column>
    </el-table>
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
      // 教师个人信息
      teacherDate: {
        TeacherName: "",
        TeacherAge: "",
        TeacherSex: "",
        TeacherPhone: "",
        TeacherEmail: "",
        TFormCollege: "",
        SubjectName: "",
        ifPresident: "",
        cheng: "",
      },
      // 教师班级成绩表格
      teachtableData: [
        {
          ClassName: "",
          grade: "",
        },
      ],
      // 弹窗扽显示隐藏
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
    this.teachermessage();
    this.getsubject();
    this.point();
  },
  methods: {
    // 页面加载调用，获取subject
    async getsubject() {
      let token = localStorage.getItem("token"); //取token
      // let email = localStorage.getItem("account");
      let email = 1;
      const { data: res } = await this.$http.get(
        `/api/teacher/returnsubject?token=` + token + "&email=" + email
      );
      // console.log(res);
      // console.log(res.data);
      localStorage.setItem("subject", res.data[0].subject);
    },
    // 教师信息展示
    async teachermessage() {
      let token = localStorage.getItem("token"); //取token
      var subject = localStorage.getItem("subject");
      let email = localStorage.getItem("account");
      //  console.log(subject);
      // var email=3378335470;
      // var subject=55555;
      const { data: res } = await this.$http.get(
        `/api/teacher/personal?token=` +
          token +
          "&email=" +
          email +
          "&subject=" +
          subject
      );
      // 渲染教师信息
      if (res.code == 100) {
        this.$message.error("当前登录非教师账号");
      } else if (res.code == 200) {
        this.teacherDate.SubjectName = res.data[0][0].SubjectName;
        this.teacherDate.TeacherAge = res.data[1][0].TeacherAge;
        this.teacherDate.TeacherName = res.data[1][0].TeacherName;
        this.teacherDate.TeacherSex = res.data[1][0].TeacherSex;
        this.teacherDate.TeacherPhone = res.data[1][0].TeacherPhone;
        this.teacherDate.TeacherEmail = res.data[1][0].TeacherEmail;
        this.teacherDate.TFormCollege = res.data[1][0].TFormCollege;
        this.teacherDate.ifPresident = res.data[1][0].ifPresident;
      }
    },

    // 平均分展示
    async point() {
      let token = localStorage.getItem("token");
      const email = localStorage.getItem("account");
      // let email = 321
      const { data: res } = await this.$http.post(
        `/api/deans/checktwo?token=` + token,
        {
          email: email,
        }
      );
      // 渲染班级平均分
      this.teachtableData = res.data;
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
  margin: 20px;
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
@media screen and (max-width: 800px) {
  .el-input {
    float: left;
    width: 100px;
  }
}
@media screen and (max-width: 670px) {
  .el-descriptions {
    font-size: 10px;
  }
  .el-input {
    width: 19vw;
  }
  .el-table-column {
    font-size: 10px;
  }
}
</style>
