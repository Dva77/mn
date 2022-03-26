<template>
  <el-card>
    <!-- 顶部搜索框 -->
    <div>
      <el-form>
        <el-form-item>
          <!-- 根据班级查询 -->
          <el-select v-model="ClassValue" placeholder="查询班级" width="100">
            <el-col :span="1">
              <el-option
                v-for="item in OptionItems"
                :key="item.value"
                :value="item.class"
              >
              </el-option>
            </el-col>
          </el-select>
          <!-- <el-col :span="1"> -->
          <el-button class="btn" @click="allStudent()" type="primary"
            >查询
          </el-button>

          <!-- 根据姓名查询 -->
          <el-input
            float="right"
            placeholder="查询姓名"
            v-model="checkname"
            class="nameinput"
          >
            <el-button
              class="btn"
              type="primary"
              plain
              slot="append"
              icon="el-icon-search"
              @click="btn_foundname"
            ></el-button>
          </el-input>

          <!-- </el-col> -->
        </el-form-item>
      </el-form>

      <el-button type="primary" @click="putout()" class="out"
        >一键导出</el-button
      >
    </div>

    <!-- 學生列表,给学生打分 -->
    <el-table
      :data="
        tableData.slice((currentPage - 1) * PageSize, currentPage * PageSize)
      "
      style="width: 100%"
    >
      <el-table-column label="姓名" prop="StudentName"> </el-table-column>
      <el-table-column label="性别" prop="StudentSex"> </el-table-column>
      <el-table-column label="年龄" prop="StudentAge"> </el-table-column>
      <el-table-column label="学号" prop="SNumber"></el-table-column>
      <el-table-column label="分数" prop="grade"> </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            type="warning"
            @click="Alter(scope.row), (dialogFormVisible = true)"
            >打分
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 弹窗 -->
    <el-dialog :visible.sync="dialogFormVisible" overflow="auto">
      <el-form :rules="formRules" ref="form" :model="form">
        输入分数
        <el-form-item prop="grade">
          <el-col :span="20">
            <el-input
              v-model="form.grade"
              oninput="value=value.replace(/\D/g,'')"
            ></el-input>
          </el-col>
        </el-form-item>
      </el-form>

      <template slot="footer" class="dialog-footer">
        <el-button type="primary" @click="changefenshu(form.grade)"
          >确 定
        </el-button>
      </template>
    </el-dialog>
    <!-- 专业总数 -->
    <div :model="number">专业总数 : {{ number }}</div>
    <!-- 分页 -->
    <div class="block">
      <el-pagination
        small
        :pager-count="5"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-size="PageSize"
        layout="total, prev, pager, next"
        :total="totalCount"
      >
      </el-pagination>
    </div>
  </el-card>
</template>

<script>
export default {
  created: function () {
    // 获取专业信息
    this.Classmessage();
    // 获取所有学生
    this.allStudent();
  },
  // 学生信息，得分
  data() {
    return {
      // 专业下拉框
      OptionItems: {},
      ClassValue: "",
      // 专业
      SFormClass: "",
      // 专业总数
      number: "",
      form: {
        grade: "",
      },
      // 查名字
      checkname: "",
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      totalCount: 1,
      // 个数选择器（可修改）
      pageSizes: 6,
      // 默认每页显示的条数（可修改）
      PageSize: 5,
      // 弹窗
      dialogTableVisible: false,
      dialogFormVisible: false,
      screenWidth: null,
      // 学生信息列表
      tableData: [
        {
          StudentName: "",
          StudentSex: "",
          StudentAge: "",
          SNumber: "",
          grade: "",
          changefenshu: "",
        },
      ],
      // 输入框验证
      formRules: {
        grade: [
          { required: true, message: "请输入分数", trigger: "blur" },
          { min: 1, max: 3, message: "请保证数字在0~100之间", trigger: "blur" },
        ],
      },
    };
  },

  methods: {
    // 加载调取专业信息
    async Classmessage() {
      const TeacherEmail = localStorage.getItem("account"); //账号
      const token = localStorage.getItem("token"); //token
      // console.log(token);
      // console.log(TeacherEmail);
      const { data: res } = await this.$http.get(
        `/api/teacher/returnclass?token=` +
          token +
          "&TeacherEmail=" +
          TeacherEmail
      );
      //专业总数
      this.number = res.data.length;
      // 渲染下拉框
      this.OptionItems = res.data;
      // 存下第一个班级
      // console.log(res.data);
      if(res.code==200){
         localStorage.setItem("class1", res.data[0].class);
      }

      // console.log(res);
      // console.log(res.data);
    },
    // 加载显示学生列表，根据班级查找到学生，没班级默认显示第一个班级
    async allStudent() {
      // const account = localStorage.getItem("account");

      if (this.ClassValue !== "") {
        var SFormClass = this.ClassValue;
      } else {
        var SFormClass = localStorage.getItem("class1");
        this.ClassValue = SFormClass;
      }

      // console.log(ClassValue);
      const token = localStorage.getItem("token"); //token
      const { data: res } = await this.$http.get(
        "/api/teacher/returnstudent?token=" +
          token +
          "&SFormClass=" +
          SFormClass
      );
      // console.log(res.data);
      if (res.code == 200) {
        (this.tableData = []), (this.totalCount = res.data.length);
        // 渲染数据
        for (let i = 0; i < res.data.length; i++) {
          // console.log(res.data[i]);
          this.tableData.push(res.data[i]);
        }
      } else {
        this.$message.error("查找错误");
      }
    },

    // 插槽
    async Alter(item) {
      localStorage.setItem("SNumber", item.SNumber);
    },
    // 根据姓名查询
    async btn_foundname() {
      let SFormClass = this.ClassValue;
      let token = localStorage.getItem("token"); //取token
      var StudentName = this.checkname;
      const { data: res } = await this.$http.post(
        `/api/teacher/dgrade?token=` + token,
        {
          SFormClass: SFormClass,
          StudentName: StudentName,
        }
      );
      if (res.code == 200 && res.data.length !== 0) {
        this.$message.success("查找成功！");
      } else if (res.code == 100) {
        this.$message.error("出错了");
      } else {
        this.$message.error("没有这个学生");
        this.allStudent();
      }
      // console.log(res.data);
      //清楚之前的数据
      this.tableData = [];
      // 页数根据数据条数改变
      this.totalCount = res.data.length;
      // 学生信息渲染
      this.tableData = res.data;
    },
    // 修改分数弹窗
    async changefenshu() {
      let token = localStorage.getItem("token");
      let SNumber = localStorage.getItem("SNumber");
      let subject = this.ClassValue;
      var grade = this.form.grade;
      if (grade < 0 || grade > 100) {
        this.$message.error("输入正确分数");
        this.form.grade = "";
        return false;
      }
      const { data: res } = await this.$http.post(
        `/api/teacher/dagain?token=` + token,
        {
          grade: grade,
          SNumber: SNumber,
          subject: subject,
        }
      );
      // console.log(res.data);
      if (res.code == 200) {
        for (let i = 0; i < this.tableData.length; i++) {
          if (this.tableData[i].SNumber == SNumber) {
            this.tableData[i].grade = grade;
            this.$message.success("打分成功！");
            this.form.grade = "";
            this.dialogFormVisible = false;
          }
        }
      } else {
        this.$message.error("错误");
      }
      // 刷新
      // location.reload(); //出现空白页
    },
    // 分页
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 在改变每页显示的条数时，将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
    //一键导出
    putout() {
      let token = localStorage.getItem("token"); //取token
      var ClassName = this.ClassValue;
      // var ClassName='s'
      window.open(
        "http://schoolsys.wzhyuming.top/api/deans/teacherexport?token=" +
          token +
          "&ClassName=" +
          ClassName
      );
    },
  },
};
</script>

<style scoped>
.nameinput {
  width: 40vw;
}
.btn {
  margin-bottom: 20px;
}
.out {
  float: right;
}
.block {
  text-align: center;
}
@media screen and(max-width:700px) {
  .el-input {
    width: 50px;
  }
  .el-option {
    height: 20px;
  }
  .btn {
    width: 30px;
  }
}
</style>
