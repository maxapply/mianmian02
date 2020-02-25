<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <!-- 第一行 -->
        <el-row :gutter="20">
          <el-col :span="6">学&nbsp;&nbsp;&nbsp;&nbsp;科：
              <el-select v-model="searchForm.subjectID" placeholder="请选择" class="md">
                <el-option v-for="item in subjectIDList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>  
          </el-col>
          <el-col :span="6">难&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 度：
             <el-select v-model="searchForm.difficulty" placeholder="请选择" class="md">
                <el-option v-for="item in difficultyList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>  
          </el-col>
          <el-col :span="6">试题类型：
             <el-select v-model="searchForm.questionType" placeholder="请选择" class="md">
                <el-option v-for="item in questionTypeList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>  
          </el-col>
          <el-col :span="6">标签：
            <el-select v-model="searchForm.tags" placeholder="请选择" class="md">
                <el-option v-for="item in tagNameList" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-col>
        </el-row>
          <!-- 第二行 -->
        <el-row :gutter="20" style="margin-top:20px">
          <el-col :span="6">录入人：
              <el-select v-model="searchForm.user" placeholder="请选择" class="md">
                <el-option v-for="item in creatorList" :key="item.value" :label="item.username" :value="item.id"></el-option>
              </el-select>
          </el-col>
          <el-col :span="6">二级目录：
              <el-select v-model="searchForm.directorys" placeholder="请选择" class="md">
                <el-option v-for="item in directorysList" :key="item.id" :label="item.label" :value="item.value"></el-option>
              </el-select> 
          </el-col>
          <el-col :span="6">方&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 向：
              <el-select v-model="searchForm.directions" placeholder="请输入题目编号/题干" class="md">
                <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
              </el-select> 
          </el-col>
          <el-col :span="6">城市：
              <el-select @change="getCitye(searchForm.provincess)" v-model="searchForm.provincess" placeholder="选城市" class="md" style="width:83px">
                <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
              </el-select> 
              <el-select  v-model="searchForm.city" placeholder="选区县" class="md" style="width:83px">
                <el-option  v-for="item in cityslist" :key="item" :label="item" :value="item"></el-option>
              </el-select> 
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>
   
<script>
import {simple} from '@/api/hmmm/subjects.js'
import {simple as simpleList} from '@/api/hmmm/tags.js'
import {simple as usersSimple} from '@/api/base/users.js'
import {simple as directorysSimple} from '@/api/hmmm/directorys.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
  } from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsList',
  data() {
    return {
      provinces,
      directionList,
      questionTypeList,
      difficultyList,
      cityslist: [],
      tagNameList: [],
      creatorList: [],
      subjectIDList: [],
      directorysList: [],
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: '',
        tags: '',
        user: '',
        directorys: '',
        directions: '',
        provincess: '',
        city: ''
      }
    }
  },
  created () {
    this.getsubjectIDList()
    this.gettagNameList()
    this.getcreatorList()
    this.getdirectorysList()
  },
  methods: {
    async getsubjectIDList () {
      const result = await simple()
      this.subjectIDList = result.data
    },
    async gettagNameList () {
      const result = await simpleList()
      this.tagNameList = result.data
    },
    async getcreatorList () {
      const result = await usersSimple()   
      this.creatorList = result.data
    },
    async getdirectorysList () {
      const result = await directorysSimple()
      this.directorysList = result.data
    },
    getCitye (pname) {
      this.searchForm.city = ''
      this.cityslist = citys(pname)
    }
  }
}
</script>

<style scoped>
.md {
  width: 170px;
}
</style>
