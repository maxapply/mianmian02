<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
            <el-button type="primary" @click="$router.push('/questions/new')" size="mini">{{$t('questions.newadd')}}</el-button>
            <el-button type="danger" size="mini">{{$t('questions.manyadd')}}</el-button>
        <!-- 第一行 -->
        <el-row :gutter="20" style="margin-top:10px">
          <el-col :span="6">学&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 科：
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
          <el-col :span="6">标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 签：
            <el-select v-model="searchForm.tags" placeholder="请选择" class="md">
                <el-option v-for="item in tagNameList" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-col>
        </el-row>
          <!-- 第二行 -->
        <el-row :gutter="20">
          <el-col :span="6">城&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 市：
              <el-select @change="searchForm.city=''" v-model="searchForm.province" placeholder="选城市" class="md" style="width:83px">
                <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
              </el-select>
              <el-select  v-model="searchForm.city" placeholder="选区县" class="md" style="width:83px">
                <el-option  v-for="item in citys(searchForm.province)" :key="item" :label="item" :value="item"></el-option>
              </el-select>
          </el-col>
          <el-col :span="6">关&nbsp; 键 &nbsp;字：
              <el-input v-model="searchForm.keyword" placeholder="请输入题目编号/题干" class="md"></el-input>
          </el-col>
          <el-col :span="6">题目备注：
              <el-input v-model="searchForm.remarks" placeholder="请输入" class="md"></el-input>
          </el-col>
          <el-col :span="6">企业简称：
              <el-input v-model="searchForm.shortName" placeholder="请输入" class="md"></el-input>
          </el-col>
        </el-row>
        <!-- 第三行 -->
        <el-row :gutter="20">
          <el-col :span="6">方&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 向：
              <el-select v-model="searchForm.direction" placeholder="请选择" class="md">
                <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
              </el-select>
          </el-col>
          <el-col :span="6">录&nbsp; 入&nbsp; 人：
              <el-select v-model="searchForm.creatorID" placeholder="请选择" class="md">
                <el-option v-for="item in creatorList" :key="item.value" :label="item.username" :value="item.id"></el-option>
              </el-select>
          </el-col>
          <el-col :span="6">二级目录：
              <el-select v-model="searchForm.catalogID" placeholder="请输入二级目录" class="md">
                <el-option v-for="item in directorysList" :key="item.id" :label="item.label" :value="item.value"></el-option>
              </el-select>
          </el-col>
          <el-col :span="6">
              <el-button size="mini">成功按钮</el-button>
              <el-button type="primary" size="mini">主要按钮</el-button>
          </el-col>
        </el-row>
      <el-table :data="questionsList" style="width: 100%" >
      <el-table-column type="index" label="序号" width="60"></el-table-column>
      <el-table-column prop="number" label="试题编号" ></el-table-column>
      <el-table-column prop="subject" label="学科" ></el-table-column>
      <el-table-column prop="questionType" label="题型" :formatter="questionTypeFMT"></el-table-column>
      <el-table-column prop="question" label="题干" ></el-table-column>
      <el-table-column prop="addDate" label="录入时间" >
        <template slot-scope="stData">
          {{stData.row.addDate|parseTimeByString}}
        </template>
      </el-table-column>
      <el-table-column prop="difficulty" label="难度" :formatter="difficultyListFMT"></el-table-column>
      <el-table-column prop="creator" label="录入人" ></el-table-column>
      <el-table-column prop="date" label="操作" width="200">
        <template slot-scope="stDate">
          <a href="#">预览</a>
          <a href="#">修改</a>
          <a href="#" @click.prevent="removes(stDate.row)">删除</a>
          <a href="#">加入精选</a>
        </template>
      </el-table-column>
      </el-table>
       <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="searchForm.page"
        :page-sizes="[3, 5, 10, 20]"
        :page-size="searchForm.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="tot">
      </el-pagination>
      </el-card>
    </div>
  </div>
</template>
   
<script>
import {simple} from '@/api/hmmm/subjects.js'
import {simple as simpleList} from '@/api/hmmm/tags.js'
import {list, remove} from '@/api/hmmm/questions.js'
import {simple as usersSimple} from '@/api/base/users.js'
import {simple as directorysSimple} from '@/api/hmmm/directorys.js'
import {provinces, citys} from '@/api/hmmm/citys.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
  } from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsList',
  watch: {
    searchForm: {
      handler: function(newV) {
        this.getquestionsList()
      },
      deep: true
    }
  },
  data() {
    return {
      directionList,
      questionTypeList,
      difficultyList,
      cityslist: [],
      tagNameList: [],
      creatorList: [],
      subjectIDList: [],
      directorysList: [],
      questionsList: [],
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: '',
        tags: '',
        creatorID: '',
        catalogID: '',
        direction: '',
        province: '',
        city: '',
        keyword: '',
        remarks: '',
        shortName: '',
        page: 1,
        pagesize: 3
      },
       tot: 0
    }
  },
  created () {
    this.getsubjectIDList()
    this.gettagNameList()
    this.getcreatorList()
    this.getdirectorysList()
    this.getquestionsList()
  },
  methods: {
    provinces,
    citys,
    handleSizeChange (val) {
     this.searchForm.pagesize = val
    },
    handleCurrentChange (val) {
     this.searchForm.page = val
    },
    ages (newagse) {
      this.searchForm.page = newagse
      this.getquestionsList()
    },
    difficultyListFMT (row, column, cellValue, index) {
      return this.difficultyList[cellValue - 1].label
    },
    questionTypeFMT (row, column, cellValue, index) {
     return this.questionTypeList[cellValue - 1].label
    },
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
    async getquestionsList () {
      const result = await list(this.searchForm)
      this.questionsList = result.data.items
      this.tot = result.data.counts
    },
    removes (id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          const result = await remove(id)
          this.$message.success('删除成功')
          this.getquestionsList()
          }).catch(() => {
        })
    }
  }
}
</script>

<style scoped>
.md {
  width: 170px;
}
.el-col {
  margin-bottom: 10px;
}
.el-table {
  margin-top: 20px;
}
</style>
