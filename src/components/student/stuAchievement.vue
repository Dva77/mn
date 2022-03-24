<template>
    <div>
        <!-- 卡片视图区 -->
        <el-card class="box-card">
            <!-- 搜索框与导出按钮 -->
                    <el-input placeholder="根据科目名称查询" class="input-with-select" v-model="queryInfo">
                    <el-button slot="append" icon="el-icon-search" @click="getUserListone()" type="primary"></el-button>
                    </el-input>
                    <el-button type="primary" plain @click="reflsh()">搜索重置</el-button>
                    <el-button type="primary" @click="out()">一键导出</el-button>
            <!-- 学生成绩列表 -->
            <el-table :data="tableData.slice(
              (currentPage - 1) * pageSize,
              currentPage * pageSize
            )" stripe
            :header-cell-style="{background: '#e3e6f8' ,color: '#606166' ,'text-align':'center'}"
            >
                <el-table-column label="科目" prop="SubjectName" align="center"></el-table-column>
                <el-table-column label="教师" prop="TeacherName" align="center"></el-table-column>
                <el-table-column label="分数" prop="grade" align="center"></el-table-column>
            </el-table>
            <!-- 分页 -->
            <div class="block">
            <div>
                <el-pagination background
                @current-change="handleCurrentChange"
                @size-change="handleSizeChange"
                :current-page="currentPage"
                :page-sizes="[10]"
                :page-size="pageSize"
                layout="prev, pager, next"
                :total="tableData.length">
            </el-pagination>
            </div>
                <el-pagination background
                @current-change="handleCurrentChange"
                @size-change="handleSizeChange"
                :current-page="currentPage"
                :page-sizes="[10]"
                :page-size="pageSize"
                layout="jumper"
                :total="tableData.length">
            </el-pagination>
            </div>
        </el-card>
    </div>
</template>

<script>
export default {
    data(){
        return {
            tableData: [],
            currentPage: 1,
            pageSize: 7,
            queryInfo: ''
        }
    },
    created() {
        this.getUserList()
    },
    methods: {
        // 搜索框重置按钮
        reflsh(){
            this.$router.go(0);
        },
        async getUserList() {
            // 调用个人中心接口
            let token = localStorage.getItem("token")
            var account =localStorage.getItem('account');
            const { data: res} = await this.$http.get('/api/student/personal?token='+token+'&email='+account)
            // 调用个人成绩接口
            localStorage.setItem('SNumber',res.data[0].SNumber);  //存值
            var SNumber =localStorage.getItem('SNumber');
            const { data: res2} = await this.$http.get('/api/student/grade?token='+token+'&SNumber='+SNumber)  //传值
            this.tableData = res2.data  //取值
        },
        // 一键导出
        out(){
            let token = localStorage.getItem("token");
            var SNumber =localStorage.getItem('SNumber');
            window.open("http://schoolsys.wzhyuming.top/api/deans/studentexport?token="+token+"&SNumber="+SNumber);
        },
        async getUserListone() {
            // 调用个人中心接口
            let token = localStorage.getItem("token");
            var account =localStorage.getItem('account');
            localStorage.setItem('queryInfo',this.queryInfo);  //存值
            var queryInfo =localStorage.getItem('queryInfo');
            const { data: res} = await this.$http.post('/api/student/dgrade?token='+token+'&email='+account+'&SubjectName='+queryInfo)  //传值
            if( res.code == 200 ) {
                this.tableData = res.data;  //取值
                this.$message({
                    message: '查询成功',
                    type: 'success'
        });
            } else if( res.code == 100 ){
                this.tableData = [];
                this.$message({
                    message: '查询成功',
                    type: 'success'
        });
            } else {
                this.$message({
                    message: '查询失败',
                    type: 'error'
        });
            }
        },
        handleSizeChange(val) {
            this.pageSize=val;
        },
        handleCurrentChange(val) {
            this.currentPage=val;
        }

    }
}
</script>

<style scoped>
.el-card{
    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.15) !important;
}
.input-with-select{
    width: 230px;
    margin-bottom: 10px;
}
.el-button{
    margin-bottom: 10px;
    margin-left: 10px;
}
.el-table{
    margin-top: 15px;
}
.block{
    margin-top: 30px;
    text-align: center;
}
</style>