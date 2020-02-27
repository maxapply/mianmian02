<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-form ref="addFormRefs" :model="addForm" label-width="80px">
          <!-- 学科 -->
            <el-form-item label="学科：">
              <el-select v-model="addForm.subjectID" placeholder="请选择">
                <el-option v-for="item in subjectIDList" :key="item.value" :label="item.label"  :value="item.value"></el-option>
              </el-select>
            </el-form-item>
            <!-- 目录 -->
            <el-form-item label="目录：">
              <el-select v-model="addForm.catalogID" placeholder="请选择">
                <el-option v-for="item in directorysList" :key="item.value" :label="item.label"  :value="item.value"></el-option>
              </el-select>
            </el-form-item>
            <!-- 企业 -->
            <el-form-item label="企业：">
              <el-select v-model="addForm.enterpriseID" placeholder="请选择">
                <el-option v-for="item in companysList" :key="item.id" :label="item.shortName"  :value="item.id"></el-option>
              </el-select>
            </el-form-item>
            <!-- 城市 -->
            <el-form-item label="城市：">
              <el-select @change="addForm.city=''" v-model="addForm.province" placeholder="请选择">
                <el-option  v-for="(item,k) in provinces()" :key="k" :label="item"  :value="item" ></el-option>
              </el-select>
              <!-- 区县 -->
              <el-select v-model="addForm.city" placeholder="请选择">
                <el-option v-for="(item,k) in citys(addForm.province)" :key="k" :label="item" :value="item"></el-option>
              </el-select>
            </el-form-item>
            <!-- 方向 -->
            <el-form-item label="方向：">
              <el-select v-model="addForm.direction" placeholder="请选择">
                <el-option v-for="(item,k) in directionList" :key="k" :label="item"  :value="item"></el-option>
              </el-select>
            </el-form-item>
            <!-- 题型 -->
            <el-form-item label="题型：">
              <el-radio-group v-model="addForm.questionType">
                <el-radio v-for="item in questionTypeList" :key="item.value" :label="item.value.toString()">{{item.label}}</el-radio>
              </el-radio-group>
            </el-form-item>
            <!-- 难度 -->
            <el-form-item label="难度：">
              <el-radio-group v-model="addForm.difficulty">
                <el-radio v-for="item in difficultyList" :key="item.value" :label="item.value.toString()">{{item.label}}</el-radio>
              </el-radio-group>
            </el-form-item>
            <!-- 题干 -->
            <el-form-item label="题干：">
               <el-input type="textarea" v-model="addForm.question"></el-input>
            </el-form-item>
            <!-- 选项 -->
              <el-form-item label="选项：" v-if="addForm.questionType!=='3'">
                <!-- 单选框 -->
                <template v-if="addForm.questionType==='1'"> 
                    <el-radio v-model="singleSelect" :label="0">A: 
                      <el-input type="text" v-model="addForm.options[0].title"></el-input>
                    </el-radio><br/>
                    <el-radio v-model="singleSelect" :label="1">B: 
                      <el-input type="text" v-model="addForm.options[1].title"></el-input>
                    </el-radio><br/>
                    <el-radio v-model="singleSelect" :label="2">C: 
                      <el-input type="text" v-model="addForm.options[2].title"></el-input>
                    </el-radio><br/>
                    <el-radio v-model="singleSelect" :label="3">D: 
                      <el-input type="text" v-model="addForm.options[3].title"></el-input>
                    </el-radio>
                  </template>
            <!-- 复选框 -->
                  <template v-if="addForm.questionType==='2'"> 
                        <el-checkbox v-model="addForm.options[0].isRight" :label="0">A: 
                          <el-input type="text" v-model="addForm.options[0].title"></el-input>
                        </el-checkbox><br/>
                        <el-checkbox v-model="addForm.options[1].isRight" :label="1">B: 
                          <el-input type="text" v-model="addForm.options[1].title"></el-input>
                        </el-checkbox><br/>
                        <el-checkbox v-model="addForm.options[2].isRight" :label="2">C: 
                          <el-input type="text" v-model="addForm.options[2].title"></el-input>
                        </el-checkbox><br/>
                        <el-checkbox v-model="addForm.options[3].isRight" :label="3">D: 
                          <el-input type="text" v-model="addForm.options[3].title"></el-input>
                        </el-checkbox>
                  </template> 
              </el-form-item>
            <!-- 答案解析 -->
            <el-form-item label="答案解析:">
               <el-input type="textarea" v-model="addForm.answer"></el-input>
            </el-form-item>
            <!-- 题目备注 -->
            <el-form-item label="题目备注:">
               <el-input type="textarea" v-model="addForm.remarks"></el-input>
            </el-form-item>
            <!-- 试题标签 -->
            <el-form-item label="试题标签:">
               <el-input type="text" v-model="addForm.tags"></el-input>
            </el-form-item>
            <!-- 按钮 -->
            <el-form-item >
              <el-button type="info" size="mini">取消 / 清除</el-button>
              <el-button type="primary" size="mini" @click="tianiia()">提交</el-button>
            </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import {
  direction as directionList, // 方向
  questionType as questionTypeList, // 题型
  difficulty as difficultyList // 难度
  } from '@/api/hmmm/constants.js'
import {add} from '@/api/hmmm/questions.js'
import {provinces, citys} from '@/api/hmmm/citys.js' // 城市
import {list as companys} from '@/api/hmmm/companys.js' // 企业
import {simple as directorys} from '@/api/hmmm/directorys.js' // 学科
import {simple as subject} from '@/api/hmmm/subjects.js' // 目录
export default {
  name: 'QuestionsNew',
  watch: {
    singleSelect (newV) {
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newV].isRight = true
    }
  },
  data() {
    return {
      difficultyList, // 难度
      questionTypeList, // 题型
      directionList, // 方向
      companysList: [], // 企业
      directorysList: [], // 目录
      subjectIDList: [], // 学科
      addForm: {
        subjectID: '', // 学科
        catalogID: '', // 目录
        enterpriseID: '', // 企业
        province: '', // 城市
        city: '', // 区县
        direction: '', // 方向
        questionType: '1', // 题型
        difficulty: '2', // 难度
        question: '', // 题干	
        videoURL: 'http://www.xxx.com', // 解析视频	
        answer: '', // 答案解析
        remarks: '', // 题目备注
        tags: '', // 试题标签
        options: [
          {code: 'A', title: '', img: '', isRight: false},
          {code: 'B', title: '', img: '', isRight: false},
          {code: 'C', title: '', img: '', isRight: false},
          {code: 'D', title: '', img: '', isRight: false}
        ]
      },
      singleSelect: '' // 第三方
    }
  },
  created () {
   this.getsubjectIDList()
   this.getdirectorysList()
   this.getcompanysList()
  },
  methods: {
    provinces, // 城市
    citys, // 区县
    // 学科
    async getsubjectIDList () {
      const result = await subject()
      this.subjectIDList = result.data
    },
    // 目录
    async getdirectorysList () {
      const result = await directorys()
      this.directorysList = result.data
    },
    // 企业
    async getcompanysList () {
      const result = await companys()
      this.companysList = result.data.items
    },
    // 提交
    async tianiia() {
      const result = await add(this.addForm)
      this.$message.success('添加成功')
      this.$router.push('/questions/list')
    }
  }
}
</script>

<style scoped>
.el-select {
  width: 450px;
}
.el-radio {
  margin-top: 10px;
}
.el-checkbox {
  margin-top: 10px;
}
</style>
