<template>
    <div class="box">
        <div class="boxOne">
            <div>
            <!-- 返回按钮结束 -->
            <el-button
                size="mini"
                class="btnOne"
                type="primary"
                plain
                @click="hReturn()"
            >
            返回
            </el-button>
            <!-- 返回按钮结束 -->
            </div>
            </div>
            <!-- 表格开始 -->
            <div class="boxTwo">
            <el-table
            :data="tableData.slice((currentPage - 1) * pageSize,currentPage * pageSize)"
            :stripe="true"
            border
            style="width: 90%;top:5%;left:5%"
            :header-cell-style="{fontWeight:'bold', fontSize:'14px', color:'#000'}"
            >
            <el-table-column
                align="center"
                label="学生姓名"
                prop="StudentName"
                min-width="150"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="性别"
                prop="StudentSex"
                min-width="100"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="年龄"
                prop="StudentAge"
                min-width="100"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="学号"
                prop="SNumber"
                min-width="150"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="数据操作"
                min-width="150"
                >
            <el-button
                size="mini"
                type="primary"
                @click="dialogVisible = true">
                查 看 图 表
            </el-button>
            </el-table-column>
            </el-table>
        </div>
        <!-- 表格结束 -->
        <!-- 学院总数开始 -->
        <div class="boxThree" :data="info">
        <span class="sumNum">专业总数 : {{info}}</span>
        </div>
        <!-- 学院总数结束 -->
        <div class="boxFive">
        <el-pagination
            align="center"
            small
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[5]"
            :page-size="pageSize"
            layout="total, jumper"
            :total="tableData.length">
        </el-pagination>
        </div>
        <!-- 分页开始 -->
        <div class="boxFour">
            <el-pagination
                background
                small
                :pager-count="5"
                align="center"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[5]"
                :page-size="pageSize"
                layout="prev, pager, next"
                :total="tableData.length">
            </el-pagination>
        </div>
        <!-- 分页结束 -->
        <!-- 查看详情开始 -->
        <el-dialog
            :visible.sync="dialogVisible"
            width="80%"
            >
            <div>
                <div class="pictureOne">
                    <dv-conical-column-chart :config="config" style="width:100%"/>
                </div>
                <div class="divOne">学生人数各科目平均成绩柱状图</div>
            </div>
        </el-dialog>
        <!-- 查看详情结束 -->
    </div>
</template>

<script>
export default {
    data() {
        return {
        config:{
                textColor: '#000',
                data: [
                {
                name: '',
                value: 55
            },
            {
            name: '',
            value: 90
            },
            {
            name: '',
            value: 71
            },
            {
            name: '',
            value: 66
            },
            {
            name: '',
            value: 80
            }
            ],
            showValue: true
            },
        info:{},
        // 搜索输入框
        input: '',
         // 表格数据
        tableData: [],
        // 分页
        currentPage: 1,
        pageSize:5,
        dialogVisible: false
        }
    },
        created() {
        this.getAllInfo();
        this.getAllNumber();
    },
    methods:{
        // 获取专业总数
        async getAllNumber(){
            const token = localStorage.getItem('token');
            const { data:res } = await this.$http.get('/api/speciality/allsp?token=' + token);
            this.info = res.data;
        },
        // 获取所有的数据
        async getAllInfo() {
            const token = localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/speciality/xiangqing2?token=' + token);
            this.tableData = res.data;
            this.$message({
            message: '操作成功~',
            type: 'success'
        });
        },
        hReturn(){
            this.$router.push('/marjorClass2');
        },
                // 分页
        handleSizeChange(val) {
            this.pageSize=val;
        },
        // 分页
        handleCurrentChange(val) {
            this.currentPage=val;
        },
        // 关闭弹出框
        handleClose(done) {
        this.$confirm('确认关闭？')
            .then(_ => {
                done();
            })
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

/* 搜索框 */
.inputSearch {
    border-radius: 30px;
    height: 40.8px;
    padding-left: 5%;
    width: 60%;
}

/* 搜索框边角 */
::v-deep .el-input__inner {
    border-radius: 23px;
    }

/* 搜索框按钮 */
.SearchButton {
    padding-top: 1%;
}

/* 返回按钮 */
.btnOne {
    margin-left: 5%;
}

.boxOne {
    padding: 10px;
    }

.boxTwo {
    padding: 10px;
}

.boxThree {
    padding: 10px;
}

.boxFour {
    padding-top: 10px;
}

.boxFive {
    padding-top: 10px;
}

.sumNum {
    padding-left: 5%;
}

/* 修改 表格单行的颜色 */
::v-deep .el-table tr {
	background-color: #F4F5FC;
}

.pictureOne {
    width:500vm;
    height:300px;
    border:2px solid #F4F5FC;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.divOne{
    text-align: center;
    padding: 10px;
    margin-top: 15px;
}
</style>