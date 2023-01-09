<template>
    <div>
        <el-table ref="table" :data="sections" style="width: 100%" border>
            <el-table-column align="center" label="ID" prop="id" width="100"/>
            <el-table-column align="center" label="章节号" prop="sort"/>
            <el-table-column align="center" label="标题" prop="title"/>
            <el-table-column align="center" label="创建时间" prop="createTime" width="200"/>
            <el-table-column align="center" label="更新时间" prop="updateTime" width="200"/>
            <el-table-column align="center" label="操作" width="150">
                <template #header>
                    <el-button size="mini" type="primary" @click="createSection()">新增</el-button>
                </template>
                <template #default="scope">
                    <el-dropdown trigger="click" @command="handleCommand($event, scope.row)">
                        <span class="el-dropdown-link">
                            <el-icon :size="20"><operation/></el-icon>
                        </span>
                        <template #dropdown>
                            <el-dropdown-menu>
                                <el-dropdown-item command="updateSection">编辑章节</el-dropdown-item>
                                <el-dropdown-item command="deleteSection">删除章节</el-dropdown-item>
                                <el-dropdown-item command="manageChapter">管理章节</el-dropdown-item>
                            </el-dropdown-menu>
                        </template>
                    </el-dropdown>
                </template>
            </el-table-column>
        </el-table>
    </div>


    <el-dialog  v-model="dialogVisible" :title="title" center destroy-on-close >
        <el-form :model="section" >
            <el-form-item label="章节标题" customClass="customWidth">
                <el-input v-model="section.title" />
            </el-form-item>
            <el-form-item label="章节排序">
                <el-input-number v-model="section.sort" :min="0" controls-position="right"/>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer" >
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="saveOrUpdate">确 定</el-button>
        </div>
    </el-dialog>


</template>

<script>
    import {deleteSection, getSectionsOfCourse,getSection,createSection, updateSection} from '../../utils/api'
    import SectionForm from '../../components/Section-Form.vue'

    export default {
        name: "Section-List",
        components: {SectionForm},
        data() {
            return {
                section: {},
                sections: [],
                editMode: null,
                preview: null,
                dialogVisible: false,
                courseId: null,
                title:""
            }
        },
        created() {
            this.getSections()
        },
        methods: {
            getSections() {
                getSectionsOfCourse(this.$route.query.courseId).then(result => {
                    if (result.code === '0000') {
                        this.sections = result.data
                        this.courseId = this.$route.query.courseId
                    }
                })
            },
            createSection() {
                //弹框
                //  this.dialogVisible = true
                //  this.section.courseId = this.courseId
                //弹框
                this.dialogVisible=true
                console.log("create section")
                //表单数据清空
                this.title = "添加章节"
                this.section.title = ''
                this.section.courseId = this.courseId
                this.section.sort = 0
            },

            updateSection(row) {
                //弹框
                this.dialogVisible = true
                //调用接口
                this.title = "编辑章节"
                getSection(row.id).then(response=>{
                    this.section = response.data
                })
            },
            deleteSection(row) {
                this.$confirm("确定删除？").then(() => {
                    deleteSection(row.id).then(result => {
                        if (result.code === '0000') {
                            let index = this.sections.indexOf(row)
                            this.sections.splice(index, 1)
                            this.$message.success("删除成功！")
                        }
                    }).catch(() => {
                        this.$message.warning("已取消！")
                    })
                })
            },
            handleCommand(command, row) {
                switch (command) {
                    case 'updateSection':
                        this.updateSection(row)
                        break
                    case 'deleteSection':
                        this.deleteSection(row)
                        break
                    case 'manageChapter':
                        this.$router.push({name: 'Teaching-Chapter-List', query: {sectionId: row.id}})
                        break
                }
            },
            saveOrUpdate(){
                if(!this.section.id) {
                    createSection(this.section).then(result => {
                        if (result.code === '0000') {
                            this.$message.success('创建成功！')
                        }
                    }).finally(() => this.dialogVisible = false)
                } else {
                    updateSection(this.section).then(result => {
                        if (result.code === '0000') {
                            this.$message.success('更新成功！')
                        }
                    }).finally(() => this.dialogVisible = false)
                }
                this.getSections()

            },
            handleCancel() {
                this.editMode = null
            }
        }
    }
</script>

<style>
    .el-dialog {
        display: flex;
        flex-direction: column;
        margin: 0 !important;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        --el-dialog-width:10%;
    }

    .el-dialog .el-dialog__body {
        flex: 1;
        overflow: auto;
    }

    .customWidth{
        width:80%;
    }
</style>
