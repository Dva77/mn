
﻿<template>


<template>

    <div class="box">

        <div class="boxOne">

            <!-- 搜索框开始 -->

            <div>

            <el-input

                size="mini"

                class="inputSearch"

                v-model="input"

                placeholder="根据名称查询"

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


            >重置</el-button>


            >重置</el-button>

            </div>

            <!-- 搜索框结束 -->

            <!-- 格式下载开始 -->

            <div class="divBtn">

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

                label="所属学院"

                prop="TFormCollege"

                min-width="200"

            >

            </el-table-column>

            <el-table-column

                align="center"

                label="名称"

                prop="TeacherName"

                min-width="300"

            >

            </el-table-column>

            <el-table-column

                align="center"

                label="数据操作"

                min-width="300"

                >

             <template slot-scope="scope">

            <!-- 详情按钮开始 -->

            <el-button

                size="mini"

                type="warning"

                @click="handleEdit(scope.row)">

                详 情

            </el-button>

            <!-- 详情按钮结束 -->

            <!-- 修改按钮开始 -->

            <el-button

                size="mini"

                type="primary"

                @click="handleRemove(scope.row)">

                修 改

            </el-button>

            <!-- 修改按钮结束 -->

            <!-- 删除按钮开始 -->

            <el-button

                size="mini"

                type="danger"

                icon="el-icon-delete"

                @click="handleDelete(scope.row)">

                删 除

            </el-button>

            <!-- 删除按钮结束 -->

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

        <div class="boxFive">

        <!-- 分页开始 -->

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

                label="教师姓名"

                :label-width="formLabelWidth"

                prop="teacherName"

                >

                    <el-input

                    v-model="formaAlter.teacherName"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教授班级"

                :label-width="formLabelWidth"

                prop="teacherClassA"

                >

                    <el-input

                    v-model="formaAlter.teacherClassA"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教授专业"

                :label-width="formLabelWidth"

                prop="TFormSA"

                >

                    <el-input

                    v-model="formaAlter.TFormSA"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教师年龄" :label-width="formLabelWidth"

                prop="TeacherAge"

                >

                    <el-input

                    v-model="formaAlter.TeacherAge"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教师性别"

                :label-width="formLabelWidth"

                prop="teacherGender"

                >

                <el-select

                v-model="formaAlter.teacherGender" placeholder="请选择教师的性别"

                >

                    <el-option size="mini" label="男" :value="1"></el-option>

                    <el-option size="mini" label="女" :value="0"></el-option>

                </el-select>

                </el-form-item>

                <el-form-item

                label="教师邮箱" :label-width="formLabelWidth"

                prop="TeacherEmail"

                >

                    <el-input

                    v-model="formaAlter.TeacherEmail"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教师电话"

                :label-width="formLabelWidth"

                prop="teacherPhone"

                >

                    <el-input

                    v-model="formaAlter.teacherPhone"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="所属学院" :label-width="formLabelWidth"

                prop="TeacherCollege"

                >

                    <el-input

                    v-model="formaAlter.TeacherCollege"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="教授科目" :label-width="formLabelWidth"

                prop="ProfessorSubject"

                >

                    <el-input

                    v-model="formaAlter.ProfessorSubject"

                    ></el-input>

                </el-form-item>

                <el-form-item

                label="是否院长" :label-width="formLabelWidth"

                prop="WhetherDean"

                >

                <el-select

                v-model="formaAlter.WhetherDean"

                placeholder="请选择教师是否院长"

                >

                    <el-option size="mini" label="是" :value="1"></el-option>

                    <el-option size="mini" label="否" :value="0"></el-option>

                </el-select>

                </el-form-item>

                <el-form-item

                label="教师职称" :label-width="formLabelWidth"

                prop="TeachersTitle"

                >

                    <el-input

                    v-model="formaAlter.TeachersTitle"

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

            :model="formAdd"

            :rules="DialogRulesTwo"

            ref="formAdd"

            >

                <el-form-item

                label="教师姓名"

                :label-width="formLabelWidth"

                prop="teacherNames"

                >

                <el-input

                    placeholder="请填写教师的姓名"

                    v-model="formAdd.teacherNames"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教授班级"

                :label-width="formLabelWidth"

                prop="teacherClass"

                >

                <el-input

                    placeholder="请填写教授班级"

                    v-model="formAdd.teacherClass"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教授专业"

                :label-width="formLabelWidth"

                prop="TFormS"

                >

                <el-input

                    placeholder="请填写教授专业"

                    v-model="formAdd.TFormS"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教师年龄"

                :label-width="formLabelWidth"

                prop="TeacherAges"

                >

                <el-input

                    placeholder="请填写教师的年龄"

                    v-model="formAdd.TeacherAges"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教师性别"

                :label-width="formLabelWidth"

                prop="teacherGenders"

                >

                <el-select

                v-model="formAdd.teacherGenders" placeholder="请选择教师的性别"

                >

                    <el-option size="mini" label="男" :value="1"></el-option>

                    <el-option size="mini" label="女" :value="0"></el-option>

                </el-select>

                </el-form-item>

                <el-form-item

                label="教师邮箱"

                :label-width="formLabelWidth"

                prop="TeacherEmails"

                >

                <el-input

                    placeholder="请填写教师的登录账号"

                    v-model="formAdd.TeacherEmails"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教师电话"

                :label-width="formLabelWidth"

                prop="teacherPhones"

                >

                <el-input

                    placeholder="请填写教师的电话"

                    v-model="formAdd.teacherPhones"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="所属学院" :label-width="formLabelWidth"

                prop="TeacherColleges"

                >

                <el-input

                    placeholder="请填写教师的所属学院"

                    v-model="formAdd.TeacherColleges"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="教授科目" :label-width="formLabelWidth"

                prop="ProfessorSubjects"

                >

                <el-input

                    placeholder="请填写教师所教授科目"

                    v-model="formAdd.ProfessorSubjects"

                ></el-input>

                </el-form-item>

                <el-form-item

                label="是否院长"

                :label-width="formLabelWidth"

                prop="WhetherDeans"

                >

                <el-select

                v-model="formAdd.WhetherDeans" placeholder="请选择教师是否院长"

                >

                    <el-option size="mini" label="是" :value="1"></el-option>

                    <el-option size="mini" label="否" :value="0"></el-option>

                </el-select>

                </el-form-item>

                <el-form-item

                label="教师职称" :label-width="formLabelWidth"

                prop="TeachersTitles"

                >

                <el-input

                    placeholder="请填写教师的职称"

                    v-model="formAdd.TeachersTitles"

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

        // 添加

        AddForm: false,

        // 修改后的数据

        formaAlter:{

            teacherName:'',

            TeacherAge:'',

            teacherGender:'',

            TeacherEmail:'',

            teacherPhone:'',

            TFormSA:'',

            teacherClassA:'',

            TeacherCollege:'',

            ProfessorSubject:'',

            WhetherDean:'',

            TeachersTitle:''

            },

        // 添加后的数据

        formAdd:{

            teacherNames:'',

            TeacherAges:'',

            teacherClass:'',

            TFormS:'',

            teacherGenders:'',

            TeacherEmails:'',

            teacherPhones:'',

            TeacherColleges:'',

            ProfessorSubjects:'',

            WhetherDeans:'',

            TeachersTitles:''

            },

        formLabelWidth: '80px',

        // 修改弹窗校验规则

        DialogRules:{

            teacherName:[{required:true,message:"注意教师姓名不能为空呐",trigger:"blur"}],

            TeacherAge:[{required:true,message:"注意教师年龄不能为空呐",trigger:"blur"}],

            teacherGender:[{required:true,message:"注意教师性别不能为空呐",trigger:"blur"}],

            teacherClassA:[{required:true,message:"注意教师性别不能为空呐",trigger:"blur"}],

            TFormSA:[{required:true,message:"注意教师性别不能为空呐",trigger:"blur"}],

            TeacherEmail:[{required:true,message:"注意教师邮箱不能为空呐",trigger:"blur"}],

            teacherPhone:[{required:true,message:"注意教师电话不能为空呐",trigger:"blur"}],

            TeacherCollege:[{required:true,message:"注意所属学院不能为空呐",trigger:"blur"}],

            ProfessorSubject:[{required:true,message:"注意教授科目不能为空呐",trigger:"blur"}],

            WhetherDean:[{required:true,message:"注意是否院长不能为空呐",trigger:"blur"}],

            TeachersTitle:[{required:true,message:"注意教师职称不能为空呐",trigger:"blur"}]

        },

        // 添加的校验规则

        DialogRulesTwo:{

            teacherNames:[{required:true,message:"注意教师姓名不能为空呐",trigger:"blur"}],

            TeacherAges:[{required:true,message:"注意教师年龄不能为空呐",trigger:"blur"}],

            TFormS:[{required:true,message:"注意教授专业不能为空呐",trigger:"blur"}],

            teacherClass:[{required:true,message:"注意教授班级不能为空呐",trigger:"blur"}],

            teacherGenders:[{required:true,message:"注意教师性别不能为空呐",trigger:"blur"}],

            TeacherEmails:[{required:true,message:"注意教师邮箱不能为空呐",trigger:"blur"}],

            teacherPhones:[{required:true,message:"注意教师电话不能为空呐",trigger:"blur"}],

            TeacherColleges:[{required:true,message:"注意所属学院不能为空呐",trigger:"blur"}],

            ProfessorSubjects:[{required:true,message:"注意教授科目不能为空呐",trigger:"blur"}],

            WhetherDeans:[{required:true,message:"注意是否院长不能为空呐",trigger:"blur"}],

            TeachersTitles:[{required:true,message:"注意教师职称不能为空呐",trigger:"blur"}]

        },

        }

    },

        created() {

        this.getAllNumber();

        this.getAllInfo();

    },

        methods: {

        // 获取所有教师的数量

        async getAllNumber() {

            const token=localStorage.getItem('token');

            const { data:res } = await this.$http.get('/api/teacher/allteacher?token=' + token);

            this.info = res.data

            },

        // 获取所有的学院信息

        async getAllInfo() {

            const token=localStorage.getItem('token');

            const { data:res } = await this.$http.get('/api/teacher/showall?token=' + token);

            this.tableData = res.data;

            sessionStorage.setItem('class',res.data.map(o=>{return[o.class]}));

            sessionStorage.setItem('TFormS',res.data.map(o=>{return[o.TFormS]}));

            sessionStorage.setItem('id',res.data.map(o=>{return[o.id]}));

        },

        // 详情页面

        handleEdit(item){

            sessionStorage.setItem('idid',item.id);

            sessionStorage.setItem('TeacherName',item.TeacherName);

            const id = sessionStorage.getItem('idid');

            this.$router.push({ path:'/hTeacherDetail', query: { id: id}});

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

            const { data:res } = await this.$http.post('/api/teacher/delete', {

                TeacherName: item.TeacherName,

                class: item.class,

                token: token

            });

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

        // 添加按钮

        handleAdd(){

            this.AddForm = true

        },

        // 添加提交按钮

        async Add() {

            const token=localStorage.getItem('token');

            const { data:res } = await this.$http.post('/api/teacher/add',

            {

                TeacherName: this.formAdd.teacherNames,

                TeacherAge: this.formAdd.TeacherAges,

                Title: this.formAdd.TeachersTitles,

                TeacherSex: this.formAdd.teacherGenders,

                TeacherEmail: this.formAdd.TeacherEmails,

                TeacherPhone: this.formAdd.teacherPhones,

                Subject: this.formAdd.ProfessorSubjects,

                ifPresident: this.formAdd.WhetherDeans,

                class: this.formAdd.teacherClass,

                TFormS: this.formAdd.TFormS,

                TFormCollege: this.formAdd.TeacherColleges,

                token: token

            });

            if(res.code == 200) {

            this.$message({

            message: '操作成功~',

            type: 'success'

        });

                this.getAllInfo();

            } else {

                this.$message.error('抱歉，添加数据失败，请重新操作~');

            }

        },

        // 修改按钮

        handleRemove(item){

            this.modificationForm = true;

            sessionStorage.setItem('id',item.id);

        },

        // 修改提交按钮

        async Alter() {

            const token=localStorage.getItem('token');

            const id=sessionStorage.getItem('id');

            const { data:res } = await this.$http.post('/api/teacher/modify',

            {

                id: id,

                TeacherName: this.formaAlter.teacherName,

                TeacherAge: this.formaAlter.TeacherAge,

                Title: this.formaAlter.TeachersTitle,

                TeacherSex: this.formaAlter.teacherGender,

                TeacherEmail: this.formaAlter.TeacherEmail,

                TeacherPhone: this.formaAlter.teacherPhone,

                Subject: this.formaAlter.ProfessorSubject,

                ifPresident: this.formaAlter.WhetherDean,

                class: this.formaAlter.teacherClassA,

                TFormCollege: this.formaAlter.TeacherCollege,

                TFormS: this.formaAlter.TFormSA,

                token: token

            });

            if(res.code == 200) {

            this.$message({

            message: '操作成功~',

            type: 'success'

        });

                this.getAllInfo();

            } else {

                this.$message.error('抱歉，修改数据失败，请重新操作~');

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

        // 修改 + 添加弹窗提交按钮

        submitForm(formName) {

            this.$refs[formName].validate((valid) => {

        if (valid) {

        this.formaAlter.teacherName = "";

        this.formaAlter.TeacherAge = "";

        this.formaAlter.teacherGender = "";

        this.formaAlter.TeacherEmail = "";

        this.formaAlter.teacherPhone = "";

        this.formaAlter.teacherClassA = "";

        this.formaAlter.TFormSA = "";

        this.formaAlter.TeacherCollege = "";

        this.formaAlter.ProfessorSubject = "";

        this.formaAlter.WhetherDean = "";

        this.formaAlter.TeachersTitle = "";

        this.modificationForm = false;

        this.formAdd.teacherNames = "";

        this.formAdd.TeacherAges = "";

        this.formAdd.teacherGenders = "";

        this.formAdd.TeacherEmails = "";

        this.formAdd.teacherPhones = "";

        this.formAdd.teacherClass = "";

        this.formAdd.TFormS = "";

        this.formAdd.TeacherColleges = "";

        this.formAdd.ProfessorSubjects = "";

        this.formAdd.WhetherDeans = "";

        this.formAdd.TeachersTitles = "";

        this.AddForm = false;

        } else {

        this.$message({

            message: '注意填完所有的信息哦~',

            type: 'warning'

        });

            return false;

                }

            });

        },

        // 根据姓名查询

        async SearchCollege() {

            const token=localStorage.getItem('token');

            const { data:res } = await this.$http.post('/api/teacher/show', {

                TeacherName: this.input,

                token: token

                });

            this.tableData = res.data;

            this.$message({

                showClose: true,

                message: '查询成功~',

                type: 'success'

        });

        },

        // 刷新按钮

        refresh() {

            this.$router.go(0);

        },

        // 格式下载

        async FormatDown() {

            const token=localStorage.getItem('token');

            window.open(

                "http://schoolsys.wzhyuming.top/api/teacher/export?token="+token

            );

        },

        // 导出模板导出按钮

        async out(){

            const token=localStorage.getItem('token');

            window.open("http://schoolsys.wzhyuming.top/api/teacher/export_moban?token="+token);

        },

        // 格式下载开始

        uploadUrl(){

            const token=localStorage.getItem('token');

            return (

                "http://schoolsys.wzhyuming.top/api/teacher/import" + "?token=" +token

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


            // const isLt2M = file.size / 1024 / 1024 < 10;

            if (!extension && !extension2) {

                this.$message({

                message: '上传文件只能是 xls、xlsx 格式~',


            // const isLt2M = file.size / 1024 / 1024 < 10;
            if (!extension && !extension2) {
                this.$message({
                message: '上传文件只能是 xls、xlsx 格式~',

                type: 'warning'

        });

        }


        //     if (!isLt2M) {

        //         this.$message({

        //         message: '上传模板大小不能超过 10MB~',

        //         type: 'warning'

        // });

        // }

            return extension || extension2;


        //     if (!isLt2M) {
        //         this.$message({
        //         message: '上传模板大小不能超过 10MB~',
        //         type: 'warning'
        // });
        // }
            return extension || extension2;

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

    padding: 10px;

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

    width: 40%;

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

    padding-top: 1%;

    padding-left: 5%;

    margin-bottom:5px;

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



.btnTwo {

    margin-bottom:5px;

}

</style>