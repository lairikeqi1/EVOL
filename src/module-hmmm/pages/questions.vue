<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
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
            <el-select
              placeholder="请选择"
              v-model="searchForm.province"
              @change="getCitys(searchForm.province)"
              style="width:102px"
              clearable
            >
              <el-option v-for="item in provinces()" :key="item" :label="item" :value="item">
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
      </el-card>
    </div>
    <el-table :data="questionsList" style="width:100%">
      <el-table-column label="序号" type="index"></el-table-column>
      <el-table-column label="试题编号" prop="number"></el-table-column>
      <el-table-column label="学科" prop="subject"></el-table-column>
      <el-table-column label="题型" prop="questionType" :formatter="questionTypeFMT"></el-table-column>
      <el-table-column label="题干" prop="question"></el-table-column>
      <el-table-column label="录入时间" width="170">
        <!--table表格column中获取数据信息，要使用 作用域插槽 形式-->
        <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
      </el-table-column>
      <el-table-column label="难度" prop="difficulty" :formatter="difficultyFMT"></el-table-column>
      <el-table-column label="录入人" prop="creator"></el-table-column>
      <el-table-column label="操作" width="200">
        <template slot-scope="stData">
          <a href="#">预览</a>
          <a href="#">修改</a>
          <a href="#" @click.prevent="del(stData.row)">删除</a>
          <!-- prevent：阻止a标签默认跳转执行 -->
          <a href="#">加入精选</a>
        </template>
      </el-table-column>
    </el-table>
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
import { list, remove } from '@/api/hmmm/questions' // 基础题库相关api导入
import { async } from 'q'
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
      questionsList: [], // 基础题库列表信息
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
  watch: {
    searchForm: {
      handler: function(newv, oldv) {
        this.getQuestionsList()
      },
      deep: true
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
    // 获得 基础题库 列表信息
    this.getQuestionsList()
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
    },
    // 获得基础题库列表信息
    async getQuestionsList() {
      var rst = await list(this.searchForm)
      // 把获得好的题库列表信息赋予给 questionList 成员
      this.questionsList = rst.data.items
    },
    // 对 题型 列的信息进行修饰
    // 数字 变为 汉字 显示
    // cellValue：当前列里边的每条数据信息(1/2/3)
    questionTypeFMT(row, column, cellvalue, index) {
      // return 返回的信息与列显示用的
      return this.questionTypeList[cellvalue - 1].label
    },
    // 难度数字转汉字
    difficultyFMT(row, column, cellValue) {
      return this.difficultyList[cellValue - 1]['label']
    },
    // 删除题库
    del(question) {
      this.$confirm('确认要删除此试题么？', '删除', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async() => {
        // await:保证数据删除完毕，再做刷新
        await remove(question)
        // 刷新页面
        this.getQuestionsList()
      }).catch(() => {})
    }
  }
}
</script>

<style scoped>
.el-row {
  margin-top: 10px;
}
.el-table {
  margin-top: 10px;
}
</style>
