<template>
    <el-form :model="section" :rules="rules" ref="section" label-width="60px">
        <el-form-item prop="title" label="标题">
            <el-input type="text" v-model="section.title" maxlength="100" show-word-limit/>
            <el-input-number v-model="section.sort" :min="0" controls-position="right"/>
        </el-form-item>
       
        <el-form-item class="text-right">
            <el-button size="small" @click="onSubmit('section')" type="primary" :loading="loading">
                确认
            </el-button>
            <el-button v-if="editMode" size="small" @click="onCancel">
                取消
            </el-button>
        </el-form-item>
    </el-form>
</template>


<script>
import {createSection, updateSection} from '../utils/api'

export default {
    name: "Section-Form",
    props: [
        'editMode',
        'section',
        'separatePage'
    ],
    data() {
        return {
            loading: false,
            progress: 0,
            preview: null,
            rules: {
                title: [
                    {required: true, message: '请输入标题', trigger: 'blur'},
                    {min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur'}
                ],
            }
        }
    },
    methods: {
        onSubmit(ref) {
            if (this.editMode === 'create') {
                this.$refs[ref].validate((valid) => {
                    if (valid) {
                        this.loading = true
                        this.section.courseId = this.$route.query.courseId
                        createSection(this.section).then(result => {
                            if (result.code === '0000') {
                                this.$message.success('创建成功！')
                                this.$refs['section'].resetFields()
                                this.progress = 0
                                this.preview = null
                            }
                        }).finally(() => this.loading = false)
                    }
                })
            }
            if (this.editMode === 'update') {
                this.$refs[ref].validate((valid) => {
                    if (valid) {
                        this.loading = true
                        updateSection(this.section).then(result => {
                            if (result.code === '0000') {
                                this.$message.success('更新成功！')
                            }
                        }).finally(() => this.loading = false)
                    }
                })
            }
            // this.$router.go(-1)
            let redirect = this.$route.fullPath
            console.log(this.$route.fullPath)
            console.log(this.section.courseId)
            
            console.log(redirect.search("user"))
            if(redirect.startsWith("/teaching")){
                this.$router.push({name: 'Teaching-Section-List',query: {courseId: this.section.courseId}})
            }else{
                this.$router.go({path: redirect})  
            }
        },
        onCancel() {
            if (this.separatePage) {
                this.$router.back()
            } else {
                this.$emit('cancel')
            }
        }
    }
}
</script>

<style>
.is-error .video-uploader .el-upload {
    border: 1px dashed #F56C6C;
}

.video-uploader .el-upload {
    border: 1px dashed #D9D9D9;
    margin: 0 20px 10px 0;
    border-radius: 6px;
}

.video-uploader .el-icon-plus {
    font-size: 32px;
    color: #8c939d;
    width: 200px;
    line-height: 200px;
}
</style>