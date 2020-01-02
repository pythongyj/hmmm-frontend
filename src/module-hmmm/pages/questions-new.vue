<template>
  <div class="dashboard-container">
    <div class="app-container">
      题库添加
      <el-card>
        <el-form :model="formData" :rules="rules" ref="myForm" label-width="100px">
          <el-form-item label="学科" class="listItem" prop="subjectID">
            <el-select v-model="formData.subjectID" placeholder="请选择">
              <el-option
                v-for="item in subjectNameList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录" class="listItem" prop="catalogID">
            <el-select v-model="formData.catalogID" placeholder="请选择">
              <el-option
                v-for="item in directoryNameList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业" class="listItem" prop="enterpriseID">
            <el-select v-model="formData.enterpriseID" placeholder="请选择">
              <el-option
                v-for="item in shortNameList"
                :key="item.id"
                :label="item.shortName"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市" class="listItem" prop="city">
            <el-select
              ref="myProvinces"
              class="city left"
              v-model="formData.province"
              placeholder="请选择"
            >
              <el-option v-for="item in provinces" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select class="city" v-model="formData.city" placeholder="请选择">
              <el-option v-for="item in citys" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向" class="listItem" prop="direction">
            <el-select v-model="formData.direction" placeholder="请选择">
              <el-option v-for="item in direction" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="题型" class="listItem" prop="questionType">
            <el-radio-group v-model="formData.questionType">
              <el-radio :label="'1'">单选</el-radio>
              <el-radio :label="'2'">多选</el-radio>
              <el-radio :label="'3'">简答</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="难度" class="listItem" prop="difficulty">
            <el-radio-group v-model="formData.difficulty">
              <el-radio :label="'1'">简单</el-radio>
              <el-radio :label="'2'">一般</el-radio>
              <el-radio :label="'3'">困难</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="题干" prop="question">
            <quill-editor v-model="formData.question" class="quillEditor"></quill-editor>
          </el-form-item>
          <el-form-item v-for="(item) in formData.options" :label="item.title" :key="item.code">
            <el-input style="width:500px" v-model="item.title"></el-input>
            <el-button @click.prevent="removeDomain(item.code)">删除</el-button>
          </el-form-item>
          <el-form-item ref="mySelectItem" label="选项(以下选中的选项为正确答案)" class="listItem">
            <el-button @click="addOptionItem" type="primary">增加选项与答案</el-button>
          </el-form-item>
          <el-form-item label="答案解析" prop="answer">
            <quill-editor v-model="formData.answer" class="quillEditor"></quill-editor>
          </el-form-item>
          <el-form-item label="题目备注" class="listItem" prop="remarks">
            <el-input v-model="formData.remarks"></el-input>
          </el-form-item>
          <el-form-item label="试题标签" prop="tags">
            <quill-editor v-model="formData.tags" class="quillEditor"></quill-editor>
          </el-form-item>
          <el-form-item>
            <el-row type="flex" justify="center">
              <el-button type="primary" @click="addSubject">提交</el-button>
              <el-button @click="exit">取消</el-button>
            </el-row>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import { provinces, citys } from "../../api/hmmm/citys";
import { direction, questionType, difficulty } from "../../api/hmmm/constants";
import { simple as subjectsList } from "../../api/hmmm/subjects";
import { simple as directoryNameList } from "../../api/hmmm/directorys";
import { list as companysList } from "../../api/hmmm/companys";
import { add as addQuestions } from "../../api/hmmm/questions";
import { log } from "util";
export default {
  name: "QuestionsNew",
  data() {
    return {
      formData: {
        subjectID: null, //学科名称
        catalogID: null, //目录名称
        enterpriseID: null, //企业
        province: null, //省份
        city: null, //城市
        direction: null, //方向
        questionType: "1", //题型
        difficulty: "1", //难度
        question: null, // 题干
        // addAnswer: null, //增加答案  题目选项
        answer: null, // 答案解析
        remarks: null, // 题目备注
        tags: null, // 试题标签
        videoURL: "http:www.aiqima.com",
        options: [
          // {
          //   code: -1,
          //   title: "选项1"
          // }
        ], //题目选项
        isPerfect: true
      },
      rules: {
        subjectID: [{ required: true, message: "请选择学科名称" }],
        catalogID: [{ required: true, message: "请选择目录名称" }],
        enterpriseID: [{ required: true, message: "请选择企业名称" }],
        city: [{ required: true, message: "请选择城市名称" }],
        direction: [{ required: true, message: "请选择方向" }],
        questionType: [{ required: true, message: "请选择题型" }],
        difficulty: [{ required: true, message: "请选择题目难度" }],
        stem: [{ required: true, message: "请输入题干" }],
        // addAnswer: [{ required: true, message: "请添加答案" }],
        answer: [{ required: true, message: "请输入答案解析" }],
        remarks: [{ required: true, message: "请输入题干备注" }],
        tags: [{ required: true, message: "请输入试题标签" }]
      },
      subjectNameList: [], //学科列表
      directoryNameList: [], //目录
      shortNameList: [], //企业
      provinces: provinces(), //城市
      citys: [], //城市
      direction: direction, //方向
      questionTypeRadio: {
        enable: true,
        value: null
      }, //题型单选
      questionTypeChecked: {
        enable: true,
        value: null
      }, //题型多选
      ABC: [
        "A",
        "B",
        "C",
        "D",
        "E",
        "F",
        "G",
        "H",
        "I",
        "J",
        "K",
        "L",
        "M",
        "N",
        "O",
        "P",
        "Q",
        "R",
        "S",
        "T",
        "U",
        "V",
        "W",
        "X",
        "Y",
        "Z"
      ]
      // difficulty: difficulty //难度
    };
  },
  methods: {
    // 获取 学科名称列表
    async getSubjectNameList() {
      let result = await subjectsList();
      this.subjectNameList = result.data;
    },
    // 获取 目录名称列表
    async getDirectoryNameList() {
      let result = await directoryNameList();
      this.directoryNameList = result.data;
    },
    // 获取 企业名称 列表
    async getCompanysList() {
      let result = await companysList();
      this.shortNameList = result.data.items;
    },
    // 添加按钮  点击添加选项
    addOptionItem() {
      this.formData.options.push({
        title: this.ABC[this.formData.options.length],
        code: Date.now(),
        isRight: true
      });
      // if (this.formData.questionType === 1) {
      //   console.log("单选");
      // } else if (this.formData.questionType === 2) {
      //   console.log("多选");
      // } else if (this.formData.questionType === 3) {
      //   console.log("简答");
      //   // this.$refs.mySelectItem.prototype.removeChild();
      //   console.log(this.$refs.mySelectItem.children);
      // }
      // console.log('添加');
    },
    // 删除选项
    removeDomain(id) {
      this.formData.options = this.formData.options.filter(item => {
        if (item.code === id) {
        } else {
          return item;
        }
      });
    },
    // 提交 题目  添加
    async addSubject() {
      await this.$confirm('确定添加么?')
      let result = await this.$refs.myForm.validate();
      // console.table(this.formData);
      console.log(result);
      result && (await addQuestions(this.formData));
      this.$message({
        type:'success',
        message:'添加成功'
      })
      this.exit()
    },
    // 点击取消按钮  清除已经输入的数据
    exit() {
      this.$refs.myForm.resetFields();
    }
  },
  created() {
    this.getSubjectNameList();
    this.getDirectoryNameList();
    this.getCompanysList();
  },
  watch: {
    // 监听城市的变化，根据不同的城市的变化 展示不同的县区
    "formData.province": function() {
      let data = citys(this.formData.province);
      this.citys = data;
    },
    "formData.options": function(val, oldVal) {
      //  console.log(val, oldVal);
      //  if(val!=oldVal){
      //    this.addOptionItem()
      //  }
    }
  }
};
</script>

<style lang='scss' scoped>
.listItem {
  width: 600px;
  .el-select {
    width: 100%;
  }
  .city {
    width: 240px;
    .el-select {
      width: 100%;
    }
  }
  .left {
    margin-right: 20px;
  }
}
.quillEditor {
  width: 700px;
  height: 300px;
  margin-bottom: 100px;
}
</style>
