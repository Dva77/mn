<template>
    <el-card class="box-card">
        <el-row>
           <el-col :span="5" class="search">
               <el-input v-model="input" placeholder="请输入内容"></el-input>
           </el-col>
            <el-button icon="el-icon-search" type="primary" @click="searchTeaName(input)"></el-button>
        </el-row>
        <el-table
          :data="tableData.slice((currentPage -1)*pageSize,currentPage*pageSize)"
          :stripe="true"
          align="center"
          style="width: 100%">
          <el-table-column
            label="专业名称"
            width="350">
            <template slot-scope="scope">
              <span style="margin-left: 10px">{{ scope.row.SpName }}</span>
            </template>
          </el-table-column>
          <el-table-column
            label="人数"
            width="200">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.studentnumber }}</el-tag>
            </template>
          </el-table-column>
           <el-table-column
            label="开设班级数"
            width="200">
            <template slot-scope="scope">
              <el-tag size="medium">{{ scope.row.classnumber }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="200">
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="primary"
                @click="schooleDetail( scope.row);">详细</el-button>
            </template>
          </el-table-column> 
        </el-table>
        <el-row  class="teanum">
          专业总数：{{teanum}}
        </el-row>
        <!-- 修改弹窗 -->
        <el-dialog title="修改信息" :visible.sync="changepop" width="50%" @close="ChangeClose">
          <el-form :model="changeForm" ref="ChangeRef" label-width="80px"  :rules="lookFormRules" width="200%" >
              <el-form-item label="修改教师名字"  label-width="160px"  prop="oldpass">
              <el-input v-model="changeForm.newname" ></el-input>
              </el-form-item>
              <el-form-item label="教授班级"  label-width="160px"  prop="newpass">
              <el-input v-model="changeForm.newclass" ></el-input>
              </el-form-item>   
          </el-form>
          <el-row type="flex" justify="center"> 
              <el-button type="danger" @click="changepop=false;ChangeClose()">退出</el-button>
          <el-button type="success" @click="changepop=false;change()">修改</el-button>
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
      this.getUserList()
    },
    data() {
      return {
        // 搜索
        input:"",
        // 分页
        // 分页
        currentPage:1,
        pageSize:8,
        // 教师数量
        teanum:'',
        // 表格
        tableData: [],
         // 按钮
        changepop:false,
        // 密码
        changeForm:
            {
                newname:'帅比',
                newclass:'数媒技术'
            },
        
        // 密码规则
        lookFormRules: {
         oldpass: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 3, max: 10, message: "长度在 3 到 10 个字符", trigger: 'blur'}
         ],
         newpass: [
           { required: true, message: "不能为空", trigger: 'blur' },
           { min: 3, max: 10, message: "长度在 3 到 10 个字符", trigger: 'blur'}
         ],
      
      },
      }
    },
    methods: {
        // 表格
      handleEdit(index, row) {
        // console.log(index, row);
        this.$router.push('/xueyuan_detail');
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
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.get('/api/deans/name?token='+token)
       
        if( res.code !== 200 ) {
        //  console.log('获取用户列表失败！')
        }
        this.tableData = res.data
        this.teanum = res.data.length
        // console.log(res);
      },
      // 专业详细
      schooleDetail(val){
        window.localStorage.setItem("spName",val.SpName);
        this.$router.push('/xueyuan_detail')
      },
       // 搜索
      async searchTeaName(val) {
        const token = window.localStorage.getItem("token");
        const { data: res } = await this.$http.post('/api/deans/search?token='+token,{
          name:val
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
      studentChange(val) {
        // console.log(val);
        window.localStorage.setItem("oldStudentName",val.StudentName);
      },
        // 通过状态
      async change() {
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
      this.$message.success("修改成功！");
      },
      //重置窗口  
      ChangeClose() {
          this.$refs['ChangeRef'].resetFields();
      },
    }
}
</script>

<style scoped>
  .text {
    font-size: 14px;
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
  }
  .block {
    margin-top: 5%;
   
  }
</style>