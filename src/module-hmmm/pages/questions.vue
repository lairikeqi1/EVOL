<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row>
        <el-col :span="4">
          学科：
          <el-select v-model="searchForm.subjectID">
            <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="4">
          难度：
          <el-select v-model="searchForm.difficulty">
            <el-option
              v-for="item in difficultyList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="4">
          试题类型：
          <el-select v-model="searchForm.questionType">
            <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="4">
          标签：
          <el-select v-model="searchForm.tagsID">
            <el-option
              v-for="item in tagsIDList"
              :key="item.id"
              :value="item.id"
              :label="item.tagName"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="4">
          城市：
          <el-select v-model="searchForm.diqu">
            <el-option v-for="item in sheng" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-col>
        <el-col :span="4">
          区/县：
          <el-select v-model="searchForm.diqu">
            <el-option v-for="item in sheng" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
// 学科
// as给导入的成员设置别名
import { simple } from '@/api/hmmm/subjects'
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants' // 常量数据
import { list } from '@/api/hmmm/tags'
import { provinces, citys } from '@/api/hmmm/citys'

export default {
  name: 'QuestionsList',
  data() {
    return {
      // 定义各个搜索表单域的数据展示成员
      subjectIDList: [],
      tagsIDList: [],
      difficultyList, // 简易成员赋值(difficultyList:difficultyList)
      questionTypeList, // 简易成员赋值

      // 定义搜索数据对象
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: '',
        tagsID: '', // 标签
        diqu: ''
      }
    }
  },
  created() {
    // 学科
    this.getSubjectIDList()
    // 标签
    this.getTagsIDList()
    // console.log(this.sheng)
    console.log(this.chengshi)
    console.log(this.searchForm.diqu)
    
  },
  computed: {
    chengshi() {
      return citys(this.searchForm.diqu)
    },
    sheng() {
      return provinces()
    }
  },
  methods: {
    // 获得 学科 下拉列表数据
    async getSubjectIDList() {
      var rst = await simple()
      // console.log(rst)
      // 把获得到的学科信息赋予给 subjectIDList 成员
      this.subjectIDList = rst.data
      // console.log(rst.data)
    },
    async getTagsIDList() {
      var rst = await list()
      // console.log(rst)
      // 把获得到的学科信息赋予给 subjectIDList 成员
      this.tagsIDList = rst.data.items
      // console.log(rst.data.items)
    }
  }
}
</script>

<style scoped>
</style>
