<template>
    <div>
        <el-form
            :rules="rules"
            ref="ruleF"
            :model="ruleForm"
            label-width="120px"
            class="demo-ruleForm"
        >
            <el-form-item label="景点名" prop="name">
                <el-input
                    v-model="ruleForm.name"
                    placeholder="请输入景点的名称"
                    style="width: 500px"
                ></el-input>
            </el-form-item>
            <el-form-item label="摘要" prop="summary">
                <el-input
                    type="textarea"
                    rows="4"
                    v-model="ruleForm.summary"
                    placeholder="请输入图片的描述"
                    style="width: 500px"
                ></el-input>
            </el-form-item>
            <el-form-item label="景点图">
                <el-upload
                    ref="upload"
                    action=""
                    list-type="picture-card"
                    :on-remove="handleRemove"
                    :on-change="uploadImage"
                    :limit="1"
                    :file-list="fileList"
                    :auto-upload="false"
                >
                    <i class="el-icon-plus"></i>
                    <template #tip>
                        <div style="font-size: 12px; color: #919191">
                            单次限制上传一张图片,图片大小限制500KB
                        </div>
                    </template>
                </el-upload>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="submitForm('ruleF')">确定</el-button>
            </el-form-item>
        </el-form>
        <el-image
            :src="require('D:/upload/2022/11/16/b6fbc188-a7c8-40cb-b7e8-ccd201bf73f2.png')"
            style="width: 100px; height: 100px; display: block; margin-bottom: 20px"
        />
    </div>
</template>

<script>
import axios from 'axios';
export default {
    name: 'UploadImage',

    data() {
        return {
            fileList: [],
            dialogImageUrl: '',
            //hideUploadEdit: false, //是否隐藏上传按钮
            ruleForm: {
                name: '',
                image: '',
                summary: '',
                issue: '',
            },
            rules: {
                name: [
                    {
                        required: true,
                        message: '请输入标题',
                        trigger: 'blur',
                    },
                ],
                summary: [
                    {
                        required: true,
                        message: '请输入简介',
                        trigger: 'blur',
                    },
                ],
            },
        };
    },
    methods: {
        //上传图片的方法
        uploadImage(file, filelist) {
            //console.log(file);
            let fd = new FormData();
            fd.append('file', file.raw); //传给后台接收的名字 file
            axios
                .post('/api/upload/image', fd, {
                    headers: { 'Content-Type': 'multipart/form-data' },
                })
                .then(response => {
                    //上传成功后返回的数据,
                    console.log('上传图片到:' + response.data);
                    // 将图片地址给到表单.
                    this.ruleForm.image = response.data;
                });
        },
        //移除图片功能
        handleRemove(file, fileList) {
            console.log(file, fileList);
        },
        //添加景点功能
        submitForm(formName) {
            let ruleForm = this.ruleForm;
            this.$refs[formName].validate(valid => {
                if (valid) {
                    axios.post('/api/attraction-add', ruleForm).then(resp => {
                        console.log(resp.data);
                        if (resp.data.code == 200) {
                            ElMessage.success(resp.data.msg);
                            this.resetForm(formName);
                            this.$router.push('attractions-list');
                        }
                    });
                } else {
                    ElMessage.error('不能为空!');
                    return false;
                }
            });
        },
        //提交后重置上传界面表单
        resetForm(formName) {
            this.$refs.upload.clearFiles();
            this.$refs[formName].resetFields();
        },
    },
};
</script>

<style scoped>
.hide >>> .el-upload--picture-card {
    display: none;
}
</style>
