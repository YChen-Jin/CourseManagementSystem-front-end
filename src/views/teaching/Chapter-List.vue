<template>
    <el-button type="primary" size="mini" @click="createChapter(sectionId)" plain round>
        新建小节
    </el-button>
    <el-button type="primary" size="mini" @click="back(sectionId)" plain round>
        返回章节
    </el-button>
    <ul>
        <li v-for="(chapter, index) in chapters" class="list">
            <el-row :gutter="20">
                <el-col :span="22">
                    <span class="chapter-icon">
                        <el-icon v-if="chapter.type ==='video'"><video-play/></el-icon>
                        <el-icon v-if="chapter.type ==='text'"><document/></el-icon>
                        第{{ index + 1 }}节：{{ chapter.title }}
                    </span>
                </el-col>
                <el-col :offset="1" :span="1">
                    <el-dropdown @command="handleCommand($event, chapter)" trigger="click">
                        <span class="el-dropdown-link">
                            <el-icon :size="20"><operation/></el-icon>
                        </span>
                        <template #dropdown>
                            <el-dropdown-menu>
                                <el-dropdown-item command="updateChapter">编辑小节</el-dropdown-item>
                                <el-dropdown-item command="deleteChapter">删除小节</el-dropdown-item>
                            </el-dropdown-menu>
                        </template>
                    </el-dropdown>
                </el-col>
            </el-row>
        </li>
    </ul>
</template>

<script>
    import {deleteChapter, getChaptersOfCourse,getChaptersOfSection,getSection} from '../../utils/api'

    export default {
        name: "Chapter-List",
        data() {
            return {
                sectionId: this.$route.query.sectionId,
                chapters: [],
            }
        },
        created() {
            this.getChapters()

        },
        methods: {
            getChapters() {
                // getChaptersOfCourse(this.courseId).then(result => {
                //     if (result.code === '0000') {
                //         this.chapters = result.data
                //     }
                // })

                getChaptersOfSection(this.$route.query.sectionId).then(result => {
                    if (result.code === '0000') {
                        this.chapters = result.data
                        // this.sectionId = this.$route.query.sectionId
                    }
                })
                // this.$router.go(0)
            },
            createChapter(sectionId) {
                this.$router.push({name: 'Teaching-Chapter-Create', query: {sectionId: sectionId}})
            },
            back(sectionId) {
                // this.$router.go(-1)
                getSection(sectionId).then(result=>{
                    if (result.code === '0000') {
                        console.log(result.data.courseId)
                        // this.sectionId = this.$route.query.sectionId
                        this.$router.push({name: 'Teaching-Section-List',query: {courseId: result.data.courseId}})
                    }
                })

            },
            updateChapter(chapter) {
                this.$router.push({name: 'Teaching-Chapter-Update', query: {chapterId: chapter.id}})
            },
            deleteChapter(chapter) {
                this.$confirm("确定删除？").then(() => {
                    deleteChapter(chapter.id).then(result => {
                        if (result.code === '0000') {
                            let index = this.chapters.indexOf(chapter)
                            this.chapters.splice(index, 1)
                            this.$message.success("删除成功！")
                        }
                    })
                }).catch(() => {
                    this.$message.warning("已取消！")
                })
            },
            handleCommand(command, chapter) {
                switch (command) {
                    case 'updateChapter':
                        this.updateChapter(chapter)
                        break
                    case 'deleteChapter':
                        this.deleteChapter(chapter)
                        break
                }
            }
        }
    }
</script>

<style>

</style>
