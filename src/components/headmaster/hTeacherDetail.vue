<template>
    <div class="box">
        <!-- 返回按钮 -->
            <el-button
            type="primary"
            plain
            size="mini"
            @click="goTo()"
            class="btnOne"
            >
            返回
            </el-button>
        <!-- 返回按钮结束 -->
        <!-- 表格开始 -->
        <div class="boxOne">
        <el-form
        :label-position="labelPosition"
        ref="DetailsForm"
        :model="DetailsForm"
        label-width="100px"
        style="padding-left:10%"
        :inline="true"
        >
        <el-form-item
        label="姓名"
        >
        <el-input
            v-model="DetailsForm.TeacherName"
            :disabled="true"
        >
        </el-input>
        </el-form-item>
        </el-form>
        <el-form
        ref="DetailsFormTwo"
        :model="DetailsFormTwo"
        :label-position="labelPosition"
        label-width="100px"
        style="padding-left:10%"
        :inline="true"
        >
        <el-form-item label="年龄">
            <el-input
            v-model="DetailsFormTwo.TeacherAge"
            :disabled="true"
            ></el-input>
        </el-form-item>
        <el-form-item
        label="性别"
        >
            <el-input
            v-model="DetailsFormTwo.TeacherSex"
            :disabled="true"
            ></el-input>
        </el-form-item>
        </el-form>
        <el-form
        ref="DetailsFormThree"
        :label-position="labelPosition"
        :model="DetailsFormThree"
        label-width="100px"
        style="padding-left:10%"
        :inline="true"
        >
        <el-form-item label="电话">
            <el-input
            v-model="DetailsFormThree.TeacherPhone"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        <el-form-item
        label="邮箱"
        >
            <el-input
            v-model="DetailsFormThree.TeacherEmail"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        </el-form>
        <el-form
        ref="DetailsFormFour"
        :model="DetailsFormFour"
        :label-position="labelPosition"
        label-width="100px"
        style="padding-left:10%"
        :inline="true"
        >
        <el-form-item label="所属学院">
            <el-input
            v-model="DetailsFormFour.TeacherCollege"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        <el-form-item
        label="教授科目"
        >
            <el-input
            v-model="DetailsFormFour.TeacherSubject"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        </el-form>
        <el-form
        ref="DetailsFormFive"
        :model="DetailsFormFive"
        :label-position="labelPosition"
        label-width="100px"
        style="padding-left:10%"
        :inline="true"
        >
        <el-form-item label="教师是否院长">
            <el-input
            v-model="DetailsFormFive.TeacherWhether"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        <el-form-item
        label="教授职称"
        >
            <el-input
            v-model="DetailsFormFive.TeacherRank"
            :disabled="true"
            style="width:100%;"
            ></el-input>
        </el-form-item>
        </el-form>
        </div>
        <!-- 表格结束 -->
        <!-- 成绩表格开始 -->
        <el-table
            :data="tableData"
            :stripe="true"
            style="width: 70%;left:8%;margin:2%"
            max-height="200"
            border
            :header-cell-style="{fontWeight:'bold', fontSize:'14px', color:'#000'}"
        >
        <el-table-column
            prop="class"
            label="教师教授班级"
            min-width="150"
            >
        </el-table-column>
        <el-table-column
            label="期末平均分"
            min-width="100">
            <template slot-scope="scope">
        <el-input :disabled="true" v-model="input1" @click="input(scope.row)"></el-input>
            </template>
        </el-table-column>
        </el-table>
        <!-- 成绩表格结束 -->
    </div>
</template>

<script>
export default {
data() {
    return {
        input1:'',
        labelPosition: 'left',
        DetailsForm:{
            TeacherName:'',
        },
        DetailsFormTwo:{
            TeacherAge:'',
            TeacherSex:''
        },
        DetailsFormThree:{
            TeacherPhone:'',
            TeacherEmail:''
        },
        DetailsFormFour:{
            TeacherCollege:'',
            TeacherSubject:''
        },
        DetailsFormFive:{
            TeacherWhether:'',
            TeacherRank:''
        },
        // 成绩表格
        tableData: [
        ]
    }
},
    created() {
        this.id = this.$route.query.id;
        this.getAllNumber();
        this.getAllInfo();
    },
    methods:{
    // 获取班级人数
        async getAllNumber() {
            const token = localStorage.getItem('token');
            sessionStorage.setItem('id',this.id);
            const id=sessionStorage.getItem('id');
            const { data:res } = await this.$http.post('/api/speciality/xiangqing1', {
                id: id,
                token: token
            });
            sessionStorage.setItem('Number',res.data.map(o=>{return[o.Number]}));
        },
        // 获取所有的信息
        async getAllInfo() {
            const token = localStorage.getItem('token');
            sessionStorage.setItem('id',this.id);
            const id=sessionStorage.getItem('id');
            const { data:res } = await this.$http.post('/api/teacher/xiangqing',
            {
                id: id,
                token: token
            })
            sessionStorage.setItem('subject',res.data.map(o=>{return[o.Subject]}));
            sessionStorage.setItem('idShow',res.data.map(o=>{return[o.id]}));
            sessionStorage.setItem('class1',res.data.map(o=>{return[o.class]}));
            if(res.code === 200){
                this.DetailsForm.TeacherName = res.data[0].TeacherName;
                this.DetailsFormTwo.TeacherAge = res.data[0].TeacherAge;
                if(res.data[0].TeacherSex == 0){
                    this.DetailsFormTwo.TeacherSex = "女";
                } else {
                    this.DetailsFormTwo.TeacherSex = "男";
                }
                this.DetailsFormThree.TeacherPhone = res.data[0].TeacherPhone;
                this.DetailsFormThree.TeacherEmail = res.data[0].TeacherEmail;
                this.DetailsFormFour.TeacherCollege = res.data[0].TFormCollege;
                this.DetailsFormFour.TeacherSubject = res.data[0].Subject;
                this.tableData = res.data;
                if(res.data[0].ifPresident == 0){
                    this.DetailsFormFive.TeacherWhether = "不是";
                } else {
                    this.DetailsFormFive.TeacherWhether = "是";
                }
                this.DetailsFormFive.TeacherRank = res.data[0].Title;
            const num=sessionStorage.getItem('Number');
            const idShow=sessionStorage.getItem('idShow');
            const subject=sessionStorage.getItem('subject');
            const { data:re } = await this.$http.post('/api/teacher/showav', {
                    subject: subject,
                    id: idShow,
                    num:num,
                    token: token
                });
                if(re.code === 200){
                    this.input1 = re.data.avg
                this.$message({
                showClose: true,
                message: '查询成功~',
                type: 'success'
        });
                }else{
                    this.$message.error('抱歉，查看期末平均成绩，请刷新重试~');
                }
            } else {
                this.$message.error('抱歉，查看详情失败，请刷新重试~');
            }
        },
        //    返回按钮
        goTo(){
            this.$router.replace('/hTeacher');
        }
    }
}
</script>

<style scoped>
/* 大盒子 */
.box {
    background-color: #fff;
    height: 100%;
    width: 100%;
}

/* 返回按钮 */
.btnOne {
    margin: 10px;
    right: -85%;
}

/* 修改 表格单行的颜色 */
::v-deep .el-table tr {
	background-color: #F4F5FC;
}

  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
</style>