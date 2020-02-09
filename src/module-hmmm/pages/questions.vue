<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-row class="wd">
          <el-col>
            <el-button type="primary" size="mini" @click="$router.push('/questions/new')">新增试题</el-button>
            <el-button type="danger" size="mini">批量导入</el-button>
          </el-col>
        </el-row>
        <el-row :gutter="20" class="wd">
          <el-col :span="6">
            学科：
            <el-select v-model="searchForm.subjectID" placeholder="请选择">
              <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            难&nbsp;&nbsp;&nbsp;&nbsp;度：
            <el-select v-model="searchForm.difficulty" placeholder="请选择">
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
            <el-select v-model="searchForm.questionType" placeholder="请选择">
              <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签：
            <el-select v-model="searchForm.tags" placeholder="请选择">
              <el-option
                v-for="item in tagsList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <!-- 第二行 -->
        <el-row :gutter="20" class="wd">
          <el-col :span="6">
            城市：
            <el-select
              v-model="searchForm.province"
              placeholder="城市"
              style="width:105px"
              @change="getcitys(searchForm.province)"
            >
              <!-- 城市这个是methods成员在v-for 模板中也可以使用，注意设置（）即可~！！！！！ -->
              <el-option v-for="item in provinces()" :key="item" :value="item" :label="item"></el-option>
            </el-select>
            <el-select v-model="searchForm.city" placeholder="区县" style="width:105px">
              <el-option v-for="item in cityList" :key="item" :value="item" :label="item"></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            关键字：
            <el-input
              type="text"
              v-model="searchForm.keyword"
              placeholder="请输入关键字"
              style="width:220px"
            ></el-input>
          </el-col>
          <el-col :span="6">
            题目备注：
            <el-input
              type="text"
              v-model="searchForm.remarks"
              placeholder="请输入题目备注"
              style="width:220px"
            ></el-input>
          </el-col>
          <el-col :span="6">
            企业简称：
            <el-input
              type="text"
              v-model="searchForm.shortName"
              placeholder="请输入企业简称"
              style="width:220px"
            ></el-input>
          </el-col>
        </el-row>
        <!-- 第三行 -->
        <el-row :gutter="20" class="wd">
          <el-col :span="6">
            方向：
            <el-select v-model="searchForm.direction" placeholder="请选择">
              <el-option v-for="(item,k) in directionList" :key="k" :value="item" :label="item"></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            录入人：
            <el-select v-model="searchForm.creatorID" placeholder="请选择">
              <el-option
                v-for="item in creatorIDList"
                :key="item.id"
                :value="item.id"
                :label="item.username"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            二级目录：
            <el-select v-model="searchForm.catalogID" placeholder="请选择">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            <el-button size="mini">清除</el-button>
            <el-button type="primary" size="mini">搜索</el-button>
          </el-col>
        </el-row>
        <el-table border :data="questionList" style="width:100%">
          <el-table-column label="序号" type="index" width="60"></el-table-column>
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column label="题型" :formatter="questionTypeFMT" prop="questionType"></el-table-column>
          <el-table-column label="题干" prop="question"></el-table-column>
          <el-table-column label="录入时间" prop="addDate" width="170">
            <!-- 通过插值表达式表现时间信息，并应用过滤器做格式转换 -->
            <!-- 获得当前列的信息：作用域插槽应用，具体还需要通过row衔接各个字段内容 -->
            <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
          </el-table-column>
          <el-table-column label="难度" :formatter="difficultyFMT" prop="difficulty"></el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <el-table-column label="操作" width="200">
            <!-- template是雷锋标签 -->
            <template slot-scope="stData">
              <a href="#">预览</a>
              <a href="#">修改</a>
              <!-- a标签有自己的默认行为，本身有点击事件 ，这里需要阻止a标签的的默认跳转行为， -->
              <!-- 新知识 事件修饰器  prevent  阻止标签的默认行为 -->
              <!-- 也可以把href的#号改成JavaScript：； 也可以 -->
              <a href="#" @click.prevent="del(stData.row)">删除</a>
              <a href="#">加入精选</a>
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
  </div>
</template>
<script>
// 导入api函数，获得简单学科列表信息
import { simple } from '@/api/hmmm/subjects.js'

// 导入难度的api函数 和 试题类型
// 对 导入进来的成员做别名设置，以便方便后续使用
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants.js'

// 导入api标签，获得到标签-----
import { simple as tagsSimple } from '@/api/hmmm/tags.js'

// 录入人 api-----------------
import { simple as usersSimple } from '@/api/base/users.js'

// 导入二级目录 api方法-----------
import { simple as catalogIDSimple } from '@/api/hmmm/directorys.js'

// 导入城市----
import { provinces, citys } from '@/api/hmmm/citys.js'

// 导入基础题库 api方法--------
import { list, remove } from '@/api/hmmm/questions.js'
export default {
  name: 'QuestionsList',

  data() {
    return {
      // 学科列表数据，用于给下拉列表展示使用
      subjectIDList: [],
      // difficultyList: difficulty, // 难度
      // 难度加别名之后做简易成员赋值 注意不要加【】中括号 完整版是difficultyList: difficultyList 不需要括号
      // 难度
      difficultyList,
      // 试题类型跟难度是一样的 一毛一样
      questionTypeList,
      // 学科标签
      tagsList: [],
      // 录入人
      creatorIDList: [],
      // 二级目录
      catalogIDList: [],
      // 方向
      directionList,
      // 区县------
      cityList: [],

      // 试题列表
      questionList: [],
      // 题库搜索表单数据对象
      searchForm: {
        page: 1, // 默认页码
        pagesize: 10, // 默认数据展示条数1
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 基础题库标签
        province: '', // 城市
        city: '', // 地区
        keyword: '', // 关键字
        remarks: '', // 备注
        shortName: '', // 企业简称
        directin: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  // watch监听器
  watch: {
    searchForm: {
      handler: function(newV, oldV) {
        // 重新获得试题
        this.getquestionList()
      },
      deep: true
    }
  },
  created() {
    // 学科
    this.getsubjectIDList()
    // 标签
    this.getTagsList()
    // 录入人
    this.getcreatorIDList()
    // 二级目录
    this.getcatalogIDList()
    //   // 调用城市函数
    //  this.provinces()
    // 调用基础题库
    this.getquestionList()
  },
  methods: {
    // 接收城市的函数-成员简易赋值
    // provinces:provinces,
    provinces,
    // 根据变化后的城市，获得对应的区县数据
    // pname:变化后的城市数据
    getcitys(pname) {
      // 清除旧的区县数据
      this.searchForm.city = ''
      // 走api接口
      this.cityList = citys(pname)
    },
    // 获得下拉列表 学科列表的信息
    async getsubjectIDList() {
      // axios获得学科列表信息，不要使用原生的axios
      // 调用api函数 间接执行axios
      // api函数 ; 在@/api/hmmm/questions.js 已经封装好了
      // 通过api方法，获得简单学科列表信息(即进行axios请求了)
      var aa = await simple()
      // data接收信息
      this.subjectIDList = aa.data
    },

    // 获得下拉列表 标签列表的信息-----------
    async getTagsList() {
      var aa = await tagsSimple()
      // data接收信息
      this.tagsList = aa.data
    },

    // 获得录入人列表 标签列表的信息-----------
    async getcreatorIDList() {
      var aa = await usersSimple()
      // data接收信息
      this.creatorIDList = aa.data
    },

    // 获得二级目录列表 标签列表的信息-----------
    async getcatalogIDList() {
      var aa = await catalogIDSimple()
      // data接收信息
      this.catalogIDList = aa.data
    },
    // 获得基础题库列表
    async getquestionList() {
      // 调用api，并传递参数 searchForm 然后设置数据查询条件
      let result = await list(this.searchForm)
      // 接收数据
      this.questionList = result.data.items
    },
    // 对 题型  列信息进行二次更新操作
    // row:代表当前每个数据项记录信息的，是一个对象，{id:xxx,number:xx,difficulty:xx,addDate:xx……}
    //     可以通过这个row访问到当前任何数据项目记录信息,调用形式：row.id  row.number
    // column:可以获取记录的列的新，一般不使用
    // cellValue:当前行当前列的数据项目记录信息，类似 row.questionType的内容
    // index: 代表各个记录的序号信息1/2/3/4/5……，一般不使用
    questionTypeFMT(row, column, cellValue, index) {
      // console.log(row.city)
      // return cellValue
      // 把cellValue对应的"汉字"内容返回显示
      return this.questionTypeList[cellValue - 1].label
    },
    // 难度------跟上面题型操作一样
    difficultyFMT(row, column, cellValue, index) {
      // console.log(row.city)
      // return cellValue
      // 把cellValue对应的"汉字"内容返回显示
      return this.difficultyList[cellValue - 1].label
    },
    // 删除试题
    del(question) {
      // 确认框
      this.$confirm('确认要删除该记录么?', '删除', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          // 调用remove的api方法，实现删除
          let result = await remove(question)
          // console.log(result)
          this.$message.success('删除成功')
          // 数据刷新(旧的就不显示了)
          this.getquestionList()
        })
        .catch(() => {})
    }
  }
}
</script>

<style scoped>
.wd {
  margin-bottom: 10px;
}
/* 给table设置向上的外边距 */
.el-table {
  margin-top: 20px;
}
</style>
