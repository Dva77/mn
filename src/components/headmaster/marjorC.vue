<template>
    <div class="box">
        <div class="boxOne">
            <!-- 搜索框开始 -->
            <div>
            <el-input
                class="inputSearch"
                size="mini"
                v-model="input"
                placeholder="根据专业名称查询"
            >
            </el-input>
            <!-- 搜索按钮开始 -->
            <el-button
                class="SearchButton"
                size="mini"
                icon="el-icon-search"
                type="primary"
                circle
                @click="SearchCollege()"
            ></el-button>
            <!-- 搜索按钮结束 -->
            <!-- 搜索按钮结束 -->
            <el-button
                size="mini"
                type="primary"
                plain
                @click="refresh()"
            >重置</el-button>
        </div>
            <!-- 搜索框结束 -->
            <!-- 添加按钮开始 -->
            <div class="addBtn">
            <el-button
                type="primary"
                size="mini"
                plain
                @click="btnAdd()"
            >
            添加
            </el-button>
            </div>
            <!-- 添加按钮结束 -->
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
                label="所属学院"
                prop="CollegeName"
                min-width="200"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="班级名称"
                prop="ClassName"
                min-width="200"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="人数"
                prop="Number"
                min-width="150"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="操 作"
                min-width="300"
            >
            <template slot-scope="scope">
            <el-button
                size="mini"
                type="warning"
                @click="handleEdit(scope.row)">
                详 情
            </el-button>
            <el-button
                size="mini"
                type="primary"
                @click="handleRemove(scope.row)">
                修 改
            </el-button>
            <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.row)">
                删 除
            </el-button>
            </template>
            </el-table-column>
            </el-table>
        </div>
        <!-- 表格结束 -->
        <!-- 专业总数开始 -->
        <div class="boxThree" :data="info">
        <span class="sumNum">专业总数 : {{info}}</span>
        </div>
        <!-- 专业总数结束 -->
        <div class="boxFive">
        <el-pagination
        align="center"
            small
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[6]"
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
                :page-sizes="[6]"
                :page-size="pageSize"
                layout="prev, pager, next"
                :total="tableData.length">
            </el-pagination>
        </div>
        <!-- 分页结束 -->
        <!-- 修改按钮的弹窗开始 -->
        <el-dialog
        :visible.sync="modificationForm"
        width="90%"
        center
        >
            <el-form
            :model="formaAlter"
            :rules="DialogRules"
            ref="form"
            >
                <el-form-item
                label="所属专业"
                :label-width="formLabelWidth"
                prop="majorNames"
                >
                    <el-input
                    v-model="formaAlter.majorNames"
                    ></el-input>
                </el-form-item>
                <el-form-item
                label="专业名称" :label-width="formLabelWidth"
                prop="presidentName"
                >
                    <el-input
                    v-model="formaAlter.presidentName"
                    ></el-input>
                </el-form-item>
            </el-form>
            <div class="btnDialog">
                <el-button
                type="primary"
                style="display:block;margin:0 auto"
                @click="Alter(),submitForm('form')">提 交</el-button>
            </div>
        </el-dialog>
        <!-- 修改按钮的弹窗结束 -->
        <!-- 添加按钮的弹窗开始 -->
        <el-dialog
            :visible.sync="AddForm"
            width="90%"
            center
        >
            <el-form
            :model="formAdd"
            :rules="DialogRulesTwo"
            ref="formAdd"
            >
                <el-form-item
                label="所属专业"
                :label-width="formLabelWidth"
                prop="majorNames"
                >
                    <el-input
                    v-model="formAdd.majorNames"
                    ></el-input>
                </el-form-item>
                <el-form-item
                label="专业名称" :label-width="formLabelWidth"
                prop="presidentNames"
                >
                    <el-input
                    v-model="formAdd.presidentNames"
                    ></el-input>
                </el-form-item>
            </el-form>
            <div class="btnDialog">
                <el-button
                type="primary"
                style="display:block;margin:0 auto"
                @click="Add(),submitForm('formAdd')">提 交</el-button>
            </div>
        </el-dialog>
        <!-- 添加按钮的弹窗结束 -->
    </div>
</template>

<script>
export default {
    data() {
        return {
        // 搜索输入框
        input: '',
        info:{},
        // 表格数据
        tableData: [],
        // 分页
        currentPage: 1,
        pageSize:6,
        // 修改
        modificationForm: false,
        // 添加
        AddForm: false,
        // 修改dialog
        formaAlter:{
            majorNames:'',
            presidentName:''
        },
        formLabelWidth: '80px',
        // 添加dialog
        formAdd:{
            majorNames:'',
            presidentNames:''
        },
        // 修改弹窗校验规则
        DialogRules:{
            majorNames:[{required:true,message:"注意学院名称不能为空呐",trigger:"blur"}],
            presidentName:[{required:true,message:"注意院长名称不能为空呐",trigger:"blur"}]
        },
        // 添加弹窗校验规则
        DialogRulesTwo:{
            majorNames:[{required:true,message:"注意学院名称不能为空呐",trigger:"blur"}],
            presidentNames:[{required:true,message:"注意院长名称不能为空呐",trigger:"blur"}]
        },
        }
    },
        created() {
        this.Detid = this.$route.query.Detid;
        this.getAllNumber();
        this.getAllInfo();
    },
        methods: {
        // 获取所有学院的数量
        async getAllNumber() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.get('/api/speciality/allsp?token=' + token);
            this.info = res.data
            },
        // 获取所有的学院信息
        async getAllInfo() {
            sessionStorage.setItem('Detid',this.Detid);
            const token=localStorage.getItem('token');
            const Detid=sessionStorage.getItem('Detid');
            const { data:res } = await this.$http.post('/api/speciality/xiangqing1', {
                id: Detid,
                token: token
            });
            this.tableData = res.data;
        },
        // 删除按钮
        async handleDelete(item){
            this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning',
            center: true,
            roundButton: true
        }).then(async() => {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/speciality/delete', {
                SpName: item.SpName,
                token: token
            })
            if(res.code === 200){
                this.getAllInfo();
                this.$message({
                type: 'success',
                message: '删除成功!'
            });
            } else {
                this.$message.error('删除失败!~');
            }
        }).catch(() => {
            this.$message({
            type: 'info',
            message: '已取消删除~'
            });
        });
        },
        // 修改按钮
        handleRemove(item){
            this.modificationForm = true;
            sessionStorage.setItem('Addid',item.id);
        },
        // 修改提交按钮
        async Alter() {
            const token = localStorage.getItem('token');
            const Addid = sessionStorage.getItem('Addid');
            const { data:res } = await this.$http.post('/api/speciality/modify', {
                id: Addid,
                FormCollege: this.formaAlter.majorNames,
                SpName: this.formaAlter.presidentName,
                token: token
            });
            if(res.code === 200) {
            this.$message({
            message: '操作成功~',
            type: 'success'
        });
                this.getAllInfo();
            } else {
                this.$message.error('抱歉，修改数据失败，请重新操作~')
            }
        },
        // 添加按钮
        btnAdd(){
            this.AddForm = true
        },
        // 添加提交按钮
        async Add() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/speciality/add',
            {
                SpName: this.formAdd.presidentNames,
                FormCollege: this.formAdd.majorNames,
                token: token
            });
            if(res.code == 200) {
                this.$message({
                showClose: true,
                message: '查询成功~',
                type: 'success'
        });
                this.getAllInfo();
            } else {
                this.$message.error('抱歉，添加数据失败，请重新操作~');
            }
        },
        // 分页
        handleSizeChange(val) {
            this.pageSize=val;
        },
        // 分页
        handleCurrentChange(val) {
        this.currentPage=val;
        },
        // 修改+添加弹窗提交按钮
        submitForm(formName) {
            this.$refs[formName].validate((valid) => {
        if (valid) {
        // 修改
        this.formaAlter.majorNames = "";
        this.formaAlter.presidentName = "";
        this.modificationForm = false
        // 添加
        this.formAdd.majorNames = "";
        this.formAdd.presidentNames = "";
        this.AddForm = false
        } else {
        this.$message({
            message: '注意填完所有的信息哦~',
            type: 'warning'
        });
            return false;
                }
            });
        },
        // 详情页面
        handleEdit(){
            this.$router.push('/marjorClass2Detail');
        },
        // 根据下设学院名称查询
        async SearchCollege() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/speciality/show', {
                SpName: this.input,
                token: token
                });
            if( res.code === 200){
                this.$message({
                showClose: true,
                message: '查询成功~',
                type: 'success'
        });
                this.tableData = res.data;
            } else {
                this.$message({
                    showClose: true,
                    message: '查询失败~',
                    type: 'error'
        });
            }
        },
        // 刷新按钮
        refresh() {
            this.$router.go(0);
        }
    },
}
</script>

<style scoped>
/* 大盒子 */
.box {
    background-color: #fff;
    height: 100%;
    width: 100%;
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

.boxFive {
    padding-top: 10px;
}

.boxFour {
    padding: 10px;
}

.sumNum {
    padding-left: 5%;
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

/* 添加按钮 */
.addBtn {
    padding-top: 1%;
    padding-left: 5%;
}

/* 修改 表格单行的颜色 */
::v-deep .el-table tr {
	background-color: #F4F5FC;
}

/* 添加按钮 */
.btnThree {
    top: 3%;
    right: -60%;
}
</style>