<template>
    <el-card class="box-card">
        <el-row>
            <el-col>
              <el-button plain type="danger"  @click="back()">返回</el-button>
            </el-col>
        </el-row>
        <el-row class="search_nav">
          <el-col :span="16" class="search">
              <el-input style="width:40%" v-model="input" placeholder="请输入内容"></el-input>
              <el-button icon="el-icon-search" type="primary" @click="searchTeaName(input)"></el-button>
          </el-col>
          <el-col :span="6">
             <el-upload
              class="upload-demo"
              :action="uploadUrl()"
              :on-preview="handlePreview"
              :on-remove="handleRemove"
              multiple
              :limit="3"
              :file-list="fileList">
              <el-button size="mini" plain type="primary" >一键导入</el-button>
            </el-upload>
          </el-col>
          <el-col :span="16">
              <el-button size="mini" plain type="primary" class="data_in"  @click="addpop=true">添加</el-button>
            </el-col>
          <el-col :span="6" class="search">
              <el-button size="mini" plain type="primary" @click="FormatDown()">格式下载</el-button>
          </el-col>
            
        </el-row>
         <el-row class="search_nav">
            
         </el-row>
        
        <el-table
          :data="tableData.slice((currentPage -1)*pageSize,currentPage*pageSize)"
          :stripe="true"
          align="center"
          style="width: 100%">
          <el-table-column
            label="姓名"
            width="300">
            <template slot-scope="scope">
              <span style="margin-left: 10px">{{ scope.row.StudentName }}</span>
            </template>
          </el-table-column>
          <el-table-column
            label="性别"
            width="100">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.StudentSex }}</el-tag>
            </template>
          </el-table-column>
           <el-table-column
            label="年龄"
            width="100">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.StudentAge }}</el-tag>
            </template>
          </el-table-column>
           <el-table-column
            label="学号"
            width="200">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.SNumber }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="350" align="center">
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="warning"
                @click="lookpop=true;lookGrade(scope.row)">查看成绩</el-button>
                <el-button
                size="mini"
                type="primary"
                @click="changepop=true;studentChange(scope.row)">修改</el-button>
                <el-button
                size="mini"
                type="danger"
                @click="delate(scope.row);">删除</el-button>
            </template>
          </el-table-column> 
        </el-table>
        <el-row  class="teanum">
          专业学生总数：{{teanum}}
        </el-row>
         <!-- 查看成绩弹窗 -->
        <el-dialog title="查看成绩"  align="center" :visible.sync="lookpop" width="60%">
          <el-table
            :data="LookData"
            align="center"
            style="width: 100%">
            <el-table-column
              prop="SubjectName"
              label="科目"
              width="200">
            </el-table-column>
            <el-table-column
              prop="grade"
              label="分数"
              width="200">
            </el-table-column>
            <el-table-column
              prop="TeacherName"
              label="教师"
              width="200">
            </el-table-column>
          </el-table>
        </el-dialog>
        <!-- 修改弹窗 -->
        <el-dialog title="修改信息" :visible.sync="changepop" width="80%" >
          <el-form :label-position="labelPosition" :model="changeForm" ref="lookFormRules" label-width="80px"  :rules="lookFormRules" width="200%" >
              <el-form-item label="修改姓名"  label-width="80px"  prop="change_name">
              <el-input v-model="changeForm.change_name" ></el-input>
              </el-form-item>
              <el-form-item label="修改性别"  label-width="80px"  prop="change_sex">
              <el-input v-model="changeForm.change_sex" ></el-input>
              </el-form-item>
              <el-form-item label="修改年龄"  label-width="80px"  prop="change_age">
              <el-input v-model="changeForm.change_age" ></el-input>
              </el-form-item>   
              <el-form-item label="修改学号"  label-width="80px"  prop="change_id">
              <el-input v-model="changeForm.change_id" ></el-input>
              </el-form-item>   
          </el-form>
          <el-row type="flex" justify="center"> 
              <el-button type="danger" @click="changepop=false">退出</el-button>
              <el-button type="success" @click="changepop=false;PeopleChange(changeForm)">修改</el-button>
          </el-row>
        </el-dialog>
        <!-- 添加弹窗 -->
        <el-dialog title="添加信息" :visible.sync="addpop" width="80%" >
          <el-form :label-position="labelPosition" :model="addForm" ref="addFormRules" label-width="80px"  :rules="addFormRules" width="200%" >
              <el-form-item label="添加姓名"  label-width="80px"  prop="add_studentName">
              <el-input v-model="addForm.add_studentName" ></el-input>
              </el-form-item>
              <el-form-item label="添加性别"  label-width="80px"  prop="add_sex">
              <el-input v-model="addForm.add_sex" ></el-input>
              </el-form-item>
              <el-form-item label="添加年龄"  label-width="80px"  prop="add_age">
              <el-input v-model="addForm.add_age" ></el-input>
              </el-form-item>   
              <el-form-item label="添加学号"  label-width="80px"  prop="add_id">
              <el-input v-model="addForm.add_id" ></el-input>
              </el-form-item>   
          </el-form>
          <el-row type="flex" justify="center"> 
              <el-button type="danger" @click="addpop=false">退出</el-button>
          <el-button type="success" @click="addpop=false;studentAdd(addForm)">添加</el-button>
          </el-row>
        </el-dialog>
        <!-- 分页 -->
        <div class="block" align="center">
          <el-pagination
            small
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="pageSize"
            :page-sizes="[5]"
            layout="prev, pager, next"
            :total="tableData.length"
            align="center"
            >
          </el-pagination>
        </div>
    </el-card>
</template>

<script>
export default {
    created() {
      // this.PeopleNum()
      this.getUserList()
    },
    data() {
      return {
        fileList: [],
        labelPosition:"left",
        // 搜索
        input:'',
        // 分页
        currentPage:1,
        pageSize:9,
        // 教师数量
        teanum:'0',
        // 表格
        tableData: [],
        // 查看表格
        LookData: [],
        // 添加
        addForm:{
          add_studentName:'',
          add_sex:'',
          add_age:'',
          add_id: '',
        },
         // 按钮
        changepop:false,
        lookpop:false,
        addpop:false,
        // 修改弹窗
        changeForm:
            {
               change_name:'',
               change_sex:'',
               change_age:'',
               change_id:'',
            },
        // 密码规则
        lookFormRules: {
         change_name: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 10, message: "长度在 1 到 10 个字符", trigger: 'blur'}
         ],
         change_sex: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 10, message: "长度在 1 到 10 个字符", trigger: 'blur'}
         ],
         change_age: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 3, message: "长度在 1 到 3 个字符", trigger: 'blur'}
         ],
         change_id: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 3, max: 10, message: "长度在 3 到 10 个字符", trigger: 'blur'}
         ],
      },
       // 密码规则
        addFormRules: {
         add_studentName: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 10, message: "长度在 1 到 10 个字符", trigger: 'blur'}
         ],
         add_sex: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 10, message: "长度在 1 到 10 个字符", trigger: 'blur'}
         ],
         add_age: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 1, max: 3, message: "长度在 1 到 3 个字符", trigger: 'blur'}
         ],
         add_id: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 3, max: 10, message: "长度在 3 到 10 个字符", trigger: 'blur'}
         ],
      },
      }
    },
    methods: {
      // 返回
      back() {
          this.$router.push('/xueyuan')
      },
      // 导入文件
      uploadUrl(){
        const token=localStorage.getItem('token');
        return (
          "http://schoolsys.wzhyuming.top/api/college/import" + "?token=" +token
        );
      },
      uploadSuccess(response, file, fileList) {
        // console.log(response.code);
        if (response.code) {
            this.$message({
            message: '文件导入成功~',
            type: 'success'
          });
        } 
        else {
          this.$message.error('抱歉,文件导入失败~');
        }
      },
      uploadFalse(response, file, fileList) {
        // console.log(file);
        this.$message.error('抱歉,文件上传失败~');
      },
      // 上传前对文件的大小的判断
      beforeAvatarUpload(file) {
        const extension = file.name.split(".")[1] === "xls";
        const extension2 = file.name.split(".")[1] === "xlsx";
        const isLt2M = file.size / 1024 / 1024 < 10;
        if (!extension && !extension2 && !extension3 && !extension4) {
            this.$message({
            message: '上传模板只能是 xls、xlsx 格式~',
            type: 'warning'
          });
        }
        if (!isLt2M) {
            this.$message({
            message: '上传模板大小不能超过 10MB~',
            type: 'warning'
          });
        }
        return extension || extension2 || extension3 || (extension4 && isLt2M);
      },
      submitUpload() {
        if (this.businessType != null) {
        //触发组件的action
        this.$refs.upload.submit();
      }
        if (this.businessType == null) {
        this.businessType = "businessType不能为空~";
        }
      },
      handleRemove (file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        if (file.response.code) {
            this.$message({
            message: '此文件导入成功~',
            type: 'success'
          });
        } 
        else {
          this.$message.error('抱歉,文件导入失败~');
        }
      },
    // 格式下载结束
      // 表格
      handleDelete(index, row) {
        console.log(index, row);
      },
      // 分页
      handleSizeChange(val) {
        this.pageSize = val;
      },
      handleCurrentChange(val) {
        this.currentPage = val;
      },
      // 获取所有人显示
      async getUserList() {
        // console.log(val);
        const token = window.localStorage.getItem("token");
        const spName = window.localStorage.getItem("spName");
        const { data: res } = await this.$http.post('/api/deans/details?token='+token,{
          name: spName
        })
       
        if( res.code !== 200 ) {
         console.log('获取用户列表失败！')
        }
        // console.log(res);
        this.teanum = res.data.length

        const arr = res.data
        arr.forEach(item => {
          if(item.StudentSex === 1){
            item.StudentSex = '男'
          }
          else {
            item.StudentSex = '女'
          }
        })
        this.tableData = arr

      },
      // 查看成绩
      async lookGrade(val) {
        // console.log(val);
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/grade?token='+token,{
          SNumber: val.SNumber
        })
        if( res.code !== 200 ) {
          return this.$message.error("查询失败")
        }
        // console.log(res);
        this.LookData = res.data
      },
      // 搜索
      async searchTeaName(val) {
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/studentsearch?token='+token,{
          studentname:val
        })
       if(val=='') {
          return this.getUserList()
       }
        if( res.code !== 200 ) {
          return this.$message.error("搜索失败")
        }
        // console.log(res);
        this.tableData = res.data
        this.$message.success("搜索成功！");
      },
      // 修改获取值
      studentChange(val) {
        // console.log(val);
        window.localStorage.setItem("oldStudentName",val.StudentName);
      },
      // 人员信息修改
      async PeopleChange(val) {
        const oldName = window.localStorage.getItem("oldStudentName");
        // console.log(oldName)
        const ConfirmResult = await this.$confirm(
          "此操作将修改表单状态, 是否继续?",
          "提示",
          {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
            center:true,
          }
        ).catch((err) => err);
        if (ConfirmResult !== "confirm") {
          return this.$message.info("已取消修改");
        }
        var sex = 0
        if(val.change_sex=='男')
        {
          sex = 1;
        }
        else(val.change_sex == "女")
        {
          sex = 0;
        }
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/studentmodify?token='+token,{
          newstudentname: val.change_name,
          age: val.change_age,
          sex: sex,
          id: val.change_id,
          oldstudentname:oldName
        })
        if( res.code !== 200 ) {
          // console.log(res);
          return this.$message.error("修改失败")
        }
        // console.log(res);
        this.getUserList();
        val.change_sex = ''
        val.change_name = ''
        val.change_age = ''
        val.change_id = ''

        this.$message.success("修改成功！");
      },
      // 删除弹窗
      async delate(val) {
        const ConfirmResult = await this.$confirm(
          "此操作将删除表单, 是否继续?",
          "提示",
          {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
            center:true,
          }
        ).catch((err) => err);
        if (ConfirmResult !== "confirm") {
          return this.$message.info("已取消");
        }
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/studentdelete?token='+token,{
          SNumber: val.SNumber
        })
        if( res.code !== 200 ) {
          return this.$message.error("删除失败")
        }
        // console.log(res);
        this.getUserList();
        this.$message.success("删除成功！");
      },
      // 格式下载
      async FormatDown() {
        const token = localStorage.getItem("token")
        // sessionStorage.setItem("CollegeName",this.tableData.map(o=>{return[o.CollegeName]}));
        // const CollegeName = sessionStorage.getItem("CollegeName")
        window.open(
          "http://schoolsys.wzhyuming.top/api/deanexportmoban?token="+token
        );
      },
       // 添加
      async studentAdd(val) {
        // console.log(val);
        var sex = 0
        if(val.add_sex=='男')
        {
          sex = 1;
        }
        else(val.add_sex == "女")
        {
          sex = 0;
        }
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/add?token='+token,{
          studentname: val.add_studentName,
          age: val.add_age,
          sex: sex,
          id: val.add_id,
        })
        if( res.code !== 200 ) {
           console.log(res);
          return this.$message.error("添加失败")
        }
        // console.log(res);
        // this.tableData = res.data
        this.getUserList()
        val.add_sex = ''
        val.add_age = ''
        val.add_id = ''
        val.add_studentName = ''
        this.$message.success("添加成功！");
      },
      //规则重置
      // ChangeClose() {
      //   this.$refs['ChangeRef'].resetFields();
      // },
    }
}
</script>

<style scoped>
  .text {
    font-size: 14px;
  }
  .data_in {
    margin-top: 2%;
     margin-left: 9%;
  }
  .item {
    padding: 18px 0;
  }

  .box-card {
    width: 100%;
  }
  .search {
      margin-left: 5%;
  }
  .teanum {
    margin-top: 2%;
    /* margin-left: 100px; */
  }
  .search_nav {
    margin-top: 30px;
    margin-bottom: 30px;
  }
  .block {
    margin-top: 5%;
   
  }
  .add {
    margin-top: -40px;
    margin-right: 40px;
    float: right;
  }
  ::v-deep .el-form-item__content {
    margin-left: 0px;
  }
</style>