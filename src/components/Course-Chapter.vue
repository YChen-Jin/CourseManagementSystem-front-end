<template>


    <!-- <ul>
        <li v-for="(chapter, index) in chapters" class="list flex-between"
            @click="viewChapter(chapter)">
            <span class="chapter-icon">
                <el-icon v-if="chapter.type ==='video'"><video-play/></el-icon>
                <el-icon v-if="chapter.type ==='text'"><document/></el-icon>
                第{{ index + 1 }}章：{{ chapter.title }}
            </span>
            <span v-if="chapter.videoTime">
                <el-icon><clock/></el-icon>{{ chapter.videoTime }}
            </span>
        </li>
    </ul> -->

    <div>
        <div v-for="(section, index) in sections" class="list" >
            <div @click="viewSection(section)">
                <h3>第{{ index + 1 }}章：{{ section.title }}</h3>
            </div>
            
            <ul >
                <li v-for="(chapter, index) in section.chapters" class="list flex-between"
                    @click="viewChapter(chapter)" v-show="section.isShow">
                    <span class="chapter-icon">
                        <el-icon v-if="chapter.type ==='video'"><video-play/></el-icon>
                        <el-icon v-if="chapter.type ==='text'"><document/></el-icon>
                        第{{ index + 1 }}节：{{ chapter.title }}
                    </span>
                    <span v-if="chapter.videoTime">
                        <el-icon><clock/></el-icon>{{ chapter.videoTime }}
                    </span>
                </li>
            </ul>
        </div>
        
    </div>

<!-- :title="chapter.title"  class="class_dialog_hospital" style="100%"-->
    <el-dialog  :title="chapter.title"  v-model="dialogVisible" width="810px"  center  destroy-on-close >
        <pre  class="text-content">  {{ chapter.textContent }}</pre>
        <video v-if="chapter.videoUrl" :src="chapter.videoUrl"  height="405" width="720"
               controls controlslist="nodownload" disablePictureInPicture/>
        <!-- <pre class="text-content">
            {{ chapter.textContent }}
        </pre> -->
    </el-dialog>
</template>

<script>
import {getSectionsOfCourse} from '../utils/api'
import {mapState} from 'vuex'

export default {
    name: "Course-Chapter",
    props: [
        'registered'
    ],
    data() {
        return {
            courseId: this.$route.params.id,
            sections: [],
            chapter: {},
            dialogVisible: false,
            isActive:false
        }
    },
    computed: mapState([
        'auth'
    ]),
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
            getSectionsOfCourse(this.courseId).then(result => {
                if (result.code === '0000') {
                    this.sections = result.data
                }
            })


        },
        viewChapter(chapter) {
            console.log(chapter)
            if (!this.auth) {
                this.$router.push({name: 'Login'})
            } else {
                if (this.registered) {
                    this.chapter = chapter
                    this.dialogVisible = true
                } else {
                    this.$message.warning("请先参加或购买课程")
                }
            }
        },
        viewSection(section){
            console.log(section)
            section.isShow = !section.isShow
        }
    }
}
</script>

<style>
.chapter-icon i {
    font-size: 20px;
}

.text-content {
    line-height: 2;
    font-size: 18px;
    color: #111212;
}



 .el-dialog.el-dialog--center {
    border-radius: 8px;
  }
 
.el-dialog--center .el-dialog__body {
  padding: 20px;
  padding-top: 0px;
  padding-bottom: 0px;
  margin-left: 20px;
  color: #606266;
  font-size: 20px;
  }


.el-dialog__header {
  /* background: #002a52; */
  text-align: center;
  font-size: 20px;
}
.el-dialog__title {
  background: aliceblue;
  font-size: 20px;
}




</style>