<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row>
        <el-col :span="6">
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
        <el-col :span="6">
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
        <el-col :span="6">
          试题类型：
          <el-select v-model="searchForm.questionType" style="width:150px">
            <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          标签:
          <el-select placeholder="请选择" v-model="searchForm.tags" style="width:135px">
            <el-option
              v-for="item in tagsList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row :gutter="10">
        <el-col :span="6">
          城市：
          <el-select placeholder="请选择" v-model="searchForm.province" @change="getCitys(searchForm.province)" style="width:102px" clearable>
            <el-option
              v-for="item in provinces()"
              :key="item"
              :label="item"
              :value="item"
            >
              <!-- 给城市设置change内容改变事件  @change="getCitys" -->
            </el-option>
            <!-- v-for遍历，既可以遍历data成员，也可以遍历methods方法，但是要给方法后边设置() 括号 -->
          </el-select>
          <el-select placeholder="请选择" v-model="searchForm.city" style="width:102px">
            <el-option v-for="item in cityList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          关键字：
          <el-input
            type="text"
            placeholder="请输入关键字"
            v-model="searchForm.keyword"
            style="width:160px"
          ></el-input>
        </el-col>
        <el-col :span="6">
          题目备注：
          <el-input
            type="text"
            placeholder="请输入备注"
            v-model="searchForm.remarks"
            style="width:160px"
          ></el-input>
        </el-col>
        <el-col :span="6">
          企业简称：
          <el-input
            type="text"
            placeholder="请输入简称"
            v-model="searchForm.shortName"
            style="width:150px"
          ></el-input>
        </el-col>
      </el-row>

      <el-row :gutter="10">
        <el-col :span="6">
          方向：
          <el-select placeholder="请选择" v-model="searchForm.direction" style="width:135px">
            <el-option v-for="item in directionList" :key="item" :value="item" :label="item"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          录入人：
          <el-select placeholder="请选择" v-model="searchForm.creatorID" style="width:135px">
            <el-option
              v-for="item in usersList"
              :key="item.id"
              :label="item.username"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          二级目录：
          <el-select placeholder="请选择" v-model="searchForm.catalogID" style="width:135px">
            <el-option
              v-for="item in catalogIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          <el-button size="mini">清除</el-button>
          <el-button type="primary" size="mini">搜索</el-button>
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
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants' // 常量数据
import { simple as tagsSimple } from '@/api/hmmm/tags' // 获取标签信息方法导入
import { simple as usersSimple } from '@/api/base/users' // 录入人
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 获取二级目录信息方法导入
// 城市
import { provinces, citys } from '@/api/hmmm/citys' // 获取 省份/城市 信息方法导入
export default {
  name: 'QuestionsList',
  data() {
    return {
      // 定义各个搜索表单域的数据展示成员
      subjectIDList: [],
      tagsList: [], // 标签
      usersList: [], // 录入人
      catalogIDList: [], // 二级目录
      cityList: [], // 区县
      difficultyList, // 简易成员赋值(difficultyList:difficultyList)
      questionTypeList, // 简易成员赋值
      directionList, // 方向

      // 搜索表单数据对象
      searchForm: {
        subjectID: '', // 科学
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 标签
        province: '', // 省份
        city: '', // 城市
        keyword: '', // 关键字
        remarks: '', // 备注
        shortname: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  created() {
    // 学科
    this.getSubjectIDList()
    // 标签
    this.getTagsList()
    // 录入人
    this.getusersList()
    // 二级目录
    this.getcatalogIDList()
  },
  methods: {
    // 获得 省份 信息，简易成员赋值
    provinces, // provinces:provinces
    // 获得 学科 下拉列表数据
    async getSubjectIDList() {
      var rst = await simple()
      // console.log(rst)
      // 把获得到的学科信息赋予给 subjectIDList 成员
      this.subjectIDList = rst.data
      // console.log(rst.data)
    },
    async getTagsList() {
      var rst = await tagsSimple()
      // promise对象
      this.tagsList = rst.data
    },
    async getusersList() {
      var rst = await usersSimple()
      // promise对象
      this.usersList = rst.data
    },
    async getcatalogIDList() {
      var rst = await directorysSimple()
      this.catalogIDList = rst.data
    },
    // 获得 城市 信息
    // 这个pname形参就代表被选中的省份信息
    // 获得 城市 信息
    // 这个pname形参就代表被选中的省份信息
    getCitys(pname) {
      this.searchForm.city = '' // 清除之前选取好的城市
      this.cityList = citys(pname)
    }
  }
}
</script>

<style scoped>
</style>
