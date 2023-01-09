<template>
  <!-- <div class="app-container">
 
    <h2 style="text-align: center;">发布新课程</h2>
 
    <el-steps :active="1" process-status="wait" align-center style="margin-bottom: 40px;">
      <el-step title="填写课程基本信息"/>
      <el-step title="创建课程大纲"/>
      <el-step title="最终发布"/>
    </el-steps>
 
  </div> -->


    <CourseForm :course="course" editMode="create" separatePage="true"/>

    <!-- <div>
        <el-button @click="previous">上一步</el-button>
        <el-button :disabled="saveBtnDisabled" type="primary" @click="next">下一步</el-button>
    </div> -->

</template>

<script>


import CourseForm from '../../components/Course-Form.vue'

export default {
    name: "Course-Create",
    components: {CourseForm},
    data() {
        return {
            course: {categories: []}
        }
    },

    methods:{
        next() {
            console.log('next')
            this.$router.push({ path: '/teaching/chapter/list?courseId='+this.courseId })
        }
    }

}
</script>


<style>
</style>