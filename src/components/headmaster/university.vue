<template>
    <div class="box">
        <div class="boxOne">
            <!-- 搜索框开始 -->
            <div>
            <el-input
                size="mini"
                class="inputSearch"
                v-model="input"
                placeholder="根据下设学院名查询"
            >
            </el-input>
            <!-- 搜索按钮开始 -->
            <el-button
                size="mini"
                class="SearchButton"
                icon="el-icon-search"
                type="primary"
                circle
                @click="SearchCollege()"
            ></el-button>
            <!-- 搜索按钮结束 -->
            <el-button
            size="mini"
            type="primary"
            plain
            @click="refresh()"
            >搜索重置</el-button>
            </div>
            <!-- 搜索框结束 -->
            <div class="divBtn">
            <!-- 格式下载开始 -->
            <el-button
            size="mini"
            type="primary"
            class="btnTwo"
            @click="FormatDown()"
            icon="el-icon-download"
            >
            格式下载
            </el-button>
            <!-- 格式下载结束 -->
            <!-- 一键导入按钮开始 -->
            <el-upload
                class="upload-demo"
                ref="upload"
                :action="uploadUrl()"
                :data="uploadData"
                name="file"
                :on-preview="handlePreview"
                :on-remove="handleRemove1"
                :file-list="fileList"
                :on-error="uploadFalse"
                :on-success="uploadSuccess"
                :before-upload="beforeAvatarUpload"
            >
            <el-button
            size="mini"
            type="primary"
            class="btnOne"
            icon="el-icon-s-promotion"
            >
            一键导入
            </el-button>
            </el-upload>
            <!-- 一键导入按钮结束 -->
            </div>
        </div>
        <!-- 表格开始 -->
        <div class="boxTwo">
            <el-table
            :data="tableData.slice((currentPage - 1) * pageSize,currentPage * pageSize)"
            :stripe="true"
            border
            style="width: 85%;top:5%;left:5%"
            :header-cell-style="{fontWeight:'bold', fontSize:'14px', color:'#000'}"
            >
            <el-table-column
                align="center"
                label="下设学院"
                prop="CollegeName"
                min-width="200"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="院长名称"
                prop="President"
                min-width="200"
            >
            </el-table-column>
            <el-table-column
                align="center"
                label="数据操作"
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
                icon="el-icon-delete"
                @click="handleDelete(scope.row)">
                删 除
            </el-button>
            </template>
            </el-table-column>
            </el-table>
        </div>
        <!-- 表格结束 -->
        <!-- 学院总数开始 -->
        <div class="boxThree" :data="info">
        <!-- 添加按钮开始 -->
            <el-button
            size="mini"
            type="primary"
            plain
            class="el-icon-edit"
            style="margin-left: 5%;"
            @click="handleAdd()"
            >
            添加
            </el-button>
            <!-- 添加按钮结束 -->
            <!-- 导出模板按钮开始 -->
            <el-button
                size="mini"
                type="primary"
                plain
                @click="out()"
            >
            导出模板
            </el-button>
            <!-- 导出模板按钮结束 -->
        </div>
        <span class="sumNum">学院总数 : {{info}}</span>
        <!-- 学院总数结束 -->
        <!-- 分页开始 -->
        <div class="boxFive">
        <el-pagination
            small
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[5]"
            :page-size="pageSize"
            layout="total, jumper"
            :total="tableData.length"
            align="center"
            >
        </el-pagination>
        </div>
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
        <!-- 修改按钮的弹窗开始 -->
        <el-dialog
        :visible.sync="modificationForm"
        width="90%"
        center
        >
            <el-form
            :model="formaAlter"
            :rules="DialogRules"
            ref="formaAlter"
            >
                <el-form-item
                label="学院名称"
                :label-width="formLabelWidth"
                prop="collegeName"
                >
                    <el-input
                    v-model="formaAlter.collegeName"
                    ></el-input>
                </el-form-item>
                <el-form-item
                label="院长名称" :label-width="formLabelWidth"
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
                @click="Alter(),submitForm('formaAlter')">提 交</el-button>
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
            :model="formaAdd"
            :rules="DialogRulesTwo"
            ref="formaAdd"
            >
            <el-form-item
                label="学院名称"
                :label-width="formLabelWidth"
                prop="collegeNames"
            >
                    <el-input
                    v-model="formaAdd.collegeNames"
                    ></el-input>
                </el-form-item>
                <el-form-item
                label="院长名称" :label-width="formLabelWidth"
                prop="presidentNames"
                >
                <el-input
                    v-model="formaAdd.presidentNames"
                    ></el-input>
                </el-form-item>
            </el-form>
            <div class="btnDialog">
                <el-button
                type="primary"
                style="display:block;margin:0 auto"
                @click="Add(),submitForm('formaAdd')">提 交</el-button>
            </div>
        </el-dialog>
        <!-- 添加按钮的弹窗结束 -->
    </div>
</template>

<script>
export default {
    data() {
        return {
            fileList:[],
        uploadData:{},
            info:{},
        // 搜索输入框
        input: '',
        // 表格数据
        tableData: [],
        // 分页
        currentPage: 1,
        pageSize:5,
        // 修改
        modificationForm: false,
        AddForm: false,
        formLabelWidth: '80px',
        // 修改后的数据
        formaAlter:{
                collegeName:"",
                presidentName:""
            },
        // 添加后的数据
        formaAdd:{
            collegeNames:"",
            presidentNames:""
        },
        // 修改弹窗校验规则
        DialogRules:{
            collegeName:[{required:true,message:"注意学院名称不能为空呐",trigger:"blur"}],
            presidentName:[{required:true,message:"注意院长名称不能为空呐",trigger:"blur"}]
        },
        // 添加弹窗校验规则
        DialogRulesTwo:{
            collegeNames:[{required:true,message:"注意学院名称不能为空呐",trigger:"blur"}],
            presidentNames:[{required:true,message:"注意院长名称不能为空呐",trigger:"blur"}]
        }
        }
    },
    created() {
        this.getAllNumber();
        this.getAllInfo();
    },
        methods: {
        // 获取所有学院的数量
        async getAllNumber() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.get('/api/college/allcollege?token=' + token);
            this.info = res.data
            },
        // 获取所有的学院信息
        async getAllInfo() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.get('/api/college/showall?token=' + token);
            this.tableData = res.data;
        },
        // 根据下设学院名称查询
        async SearchCollege() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/college/show', {
                CollegeName: this.input,
                token: token
                });
            this.tableData = res.data;
        },
        // 刷新按钮
        refresh() {
            this.$router.go(0);
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
            const { data:res } = await this.$http.post('/api/college/delete', {
                CollegeName: item.CollegeName,
                token: token
            })
            this.getAllInfo();
            if(res.code!==200){
                return this.$message.error('删除失败!~')
            }
            this.$message({
            type: 'success',
            message: '删除成功!~'
            });
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
            sessionStorage.setItem('id',item.id);
        },
        // 修改提交按钮
        async Alter() {
            const token = localStorage.getItem('token');
            const id = sessionStorage.getItem('id');
            const { data:res } = await this.$http.post('/api/college/modify', {
                id: id,
                CollegeName: this.formaAlter.collegeName,
                President: this.formaAlter.presidentName,
                token: token
            });
            if(res.code === 200) {
            this.$message({
                showClose: true,
                message: '修改成功~',
                type: 'success'
        });
                this.getAllInfo();
            } else {
                this.$message.error('抱歉，修改数据失败，请重新操作~')
            }
        },
        // 添加按钮
        handleAdd(){
            this.AddForm = true;
        },
        // 添加提交按钮
        async Add() {
            const token=localStorage.getItem('token');
            const { data:res } = await this.$http.post('/api/college/add',
            {
                CollegeName: this.formaAdd.collegeNames,
                President: this.formaAdd.presidentNames,
                token: token
            });
            if(res.code == 200) {
            this.$message({
                showClose: true,
                message: '添加成功~',
                type: 'success'
        });
                this.tableData.push(res.data);
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
        // 修改弹窗提交按钮
        submitForm(formName) {
            this.$refs[formName].validate((valid) => {
        if (valid) {
/*             this.$message({
            message: '操作成功~',
            type: 'success'
        }); */
        // 修改
        this.formaAlter.collegeName = "";
        this.formaAlter.presidentName = "";
        this.modificationForm = false;
        // 添加
        this.formaAdd.collegeNames = "";
        this.formaAdd.presidentNames = "";
        this.AddForm = false;
        } else {
            return false;
                }
            });
        },
        // 详情页面
        handleEdit(item){
            sessionStorage.setItem('idid',item.id);
            const id = sessionStorage.getItem('idid');
            this.$router.push({ path:'/universityDetail', query: { id: id}});
        },
        // 格式下载
        async FormatDown() {
            const token=localStorage.getItem('token');
            sessionStorage.setItem("CollegeName", this.tableData.map(o=>{return[o.CollegeName]}));
            const CollegeName = sessionStorage.getItem("CollegeName");
            window.open(
                "http://schoolsys.wzhyuming.top/api/college/export?token="+token+'&CollegeName='+CollegeName
            );
        },
        // 导出模板导出按钮
        async out(){
            const token=localStorage.getItem('token');
            window.open("http://schoolsys.wzhyuming.top/api/college/export_moban?token="+token);
        },
        // 格式下载开始
        uploadUrl(){
            const token=localStorage.getItem('token');
            return (
                "http://schoolsys.wzhyuming.top/api/college/import" + "?token=" +token
            );
        },
        uploadSuccess(response, file, fileList) {
            if (response.code) {
                this.$message({
                message: '文件导入成功~',
                type: 'success'
        });
            } else {
                this.$message.error('抱歉,文件导入失败~');
        }
    },
        uploadFalse(response, file, fileList) {
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
        handleRemove1 (file, fileList) {},
        handlePreview(file) {
            if (file.response.code) {
                this.$message({
                message: '此文件导入成功~',
                type: 'success'
        });
            } else {
                this.$message.error('抱歉,文件导入失败~');
        }
    }
    // 格式下载结束
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

.boxFour {
    padding-top: 10px;
}

.boxFive {
    padding-top: 10px;
}

.sumNum {
    padding-left: 5%;
    margin: 1%;
}

/* 搜索框 */
.inputSearch {
    border-radius: 30px;
    height: 40.8px;
    padding-left: 5%;
    width: 50%;
}

/* 搜索框边角 */
::v-deep .el-input__inner {
    border-radius: 23px;
    }

/* 搜索框按钮 */
.SearchButton {
    padding-top: 1%;
}

/* 两种按钮 */
.divBtn {
    width:70%;
    /* padding-top: 1%; */
    padding-left: 5%;
    margin-bottom:5px;
}

/* 添加按钮 */
/* .addBtn {
    padding-top: 1%;
    padding-left: 5%;
} */

/* 修改 表格单行的颜色 */
::v-deep .el-table tr {
	background-color: #F4F5FC;
}

.btnTwo {
    margin-bottom:5px;
}
</style>