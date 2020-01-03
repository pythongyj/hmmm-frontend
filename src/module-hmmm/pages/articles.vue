<template>
  <!-- <div class="dashboard-container">
    <div class="app-container">
      面试技巧
    <el-button type="primary" @click="$router.push({name:'articles-add'})">新增技巧</el-button>
    </div>
  </div>-->
  <el-card>
    <el-button slot="header" type="primary" @click="openDialog">新增技巧</el-button>
    <el-row type="flex" justify="start">
      <el-form inline label-width="100px">
        <el-form-item label="关键字">
          <el-input @input="changeKeyWords" v-model="keywords"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="clearKeyWords">清除</el-button>
          <el-button @click="changeKeyWords" type="primary">搜索</el-button>
        </el-form-item>
      </el-form>
    </el-row>
    <el-table :data="articlesList" style="width: 100%">
      <el-table-column prop="id" label="序号"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="visits" label="阅读数"></el-table-column>
      <el-table-column :formatter="formatterfiluter" prop="state" label="状态">
        <!-- <template slot-scope="obj">
          {{obj.row.state | states}}
        </template>-->
      </el-table-column>
      <el-table-column prop="creator" label="录入人"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="obj">
          <el-button type="text" @click="Preview(obj.row)">预览</el-button>
          <el-button @click="edit(obj.row)" type="text">修改</el-button>
          <el-button @click="deleteitem(obj.row.id)" type="text">删除</el-button>
          <el-button
            @click="enabled(obj.row.id,obj.row.state)"
            type="text"
          >{{obj.row.state===1 ? 0 :1| states}}</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog :show-close="false" title="新增文章" :visible.sync="dialogFormVisible">
      <el-form ref="myForm" :rules="rules" :model="formData">
        <el-form-item label="学科名称" prop="title" :label-width="formLabelWidth">
          <el-input v-model="formData.title" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="内容" prop="articleBody" :label-width="formLabelWidth">
          <quill-editor style="height:240px;margin-bottom: 140px;" v-model="formData.articleBody"></quill-editor>
        </el-form-item>
        <el-form-item label="视频地址" prop="videoURL" :label-width="formLabelWidth">
          <el-input v-model="formData.videoURL" placeholder="视频地址"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="exit">取 消</el-button>
        <el-button type="primary" @click="subArticlesItem">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog :show-close="false" title="预览文章" :visible.sync="dialogFormVisiblePreview">
      <el-form v-model="articlesItem">
        <el-form-item label="学科名称" :label-width="formLabelWidth">
          <el-input v-model="articlesItem.title" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="内容" :label-width="formLabelWidth">
          <el-input v-model="articlesItem.articleBody"></el-input>
        </el-form-item>
        <el-form-item label="视频地址" :label-width="formLabelWidth">
          <el-input v-model="articlesItem.videoURL" placeholder="视频地址"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-row type="flex" justify="end">
          <el-button @click="dialogFormVisiblePreview=false">取 消</el-button>
        </el-row>
      </div>
    </el-dialog>
    <el-row type="flex" justify="center">
      <el-pagination
        background
        layout="prev, pager, next"
        :current-page="page.page"
        :page-size="page.pagesize"
        @current-change="changePage"
        :total="page.counts"
      ></el-pagination>
    </el-row>
  </el-card>
</template>

<script>
import {
  list as listArticles,
  remove as removeItem,
  state as enableState,
  add as addArticles,
  update as updateArticles
} from "../../api/hmmm/articles";
export default {
  name: "ArticlesList",
  data() {
    return {
      articlesList: [],
      articlesItem: {},
      dialogFormVisible: false,
      dialogFormVisiblePreview: false,
      formLabelWidth: "100px",
      keywords: "",
      formData: {
        id: null,
        title: null,
        articleBody: null,
        videoURL: null
      },
      rules: {
        title: [
          { required: true, message: "学科名称不能为空", trigger: "blur" }
        ],
        articleBody: [
          { required: true, message: "内容不能为空", trigger: "blur" }
        ],
        videoURL: [
          { required: true, message: "视频地址不能为空", trigger: "blur" }
        ]
      },
      page: {
        page: 1,
        pagesize: 8,
        counts: 0
      }
    };
  },
  methods: {
    // 获取数据列表
    async getListArticles() {
      let params = this.getPage();
      let result = await listArticles(params);
      this.articlesList = result.data.items;
      this.page.page = parseInt(result.data.page);
      this.page.counts = parseInt(result.data.counts);
    },
    // 获取当前页面 的 当前页 值 以及 每页的数据条数
    getPage() {
      return {
        page: this.page.page,
        pagesize: this.page.pagesize
      };
    },
    // 点击分页的页码  跳转到  对应的页面
    async changePage(newpage) {
      this.page.page = newpage;
      let params = this.getPage();
      await this.getListArticles(params);
    },
    // 是否禁用
    formatterfiluter(row, column, cellValue, index) {
      if (cellValue === 1) {
        return "启用";
      } else if (cellValue === 0) {
        return "禁用";
      }
    },

    // 删除当前项
    async deleteitem(id) {
      await this.$confirm("确认删除么？");
      await removeItem({ id });
      this.getListArticles();
    },
    // 改变状态
    async enabled(id, state) {
      await this.$confirm("确定改变状态?");
      await enableState({ id, state: state ? 0 : 1 });
      this.getListArticles();
    },
    // 打开弹出层
    openDialog() {
      this.dialogFormVisible = true;
    },
    // 提交  和  预览
    async subArticlesItem() {
      await this.$refs.myForm.validate();
      this.formData.id === null
        ? await addArticles(this.formData)
        : await updateArticles(this.formData);
      this.$message({
        type: "success",
        message: "操作成功"
      });
      this.clearDialog();
      this.dialogFormVisible = false;
      this.getListArticles();
    },
    // 情况弹出框内的内容
    clearDialog() {
      this.formData = {
        id: null,
        title: null,
        articleBody: null,
        videoURL: null
      };
    },
    // 编辑当前项
    edit(data) {
      this.dialogFormVisible = true;
      this.formData.title = data.title;
      this.formData.articleBody = data.articleBody;
      this.formData.videoURL = data.videoURL;
      this.formData.id = data.id;
    },
    // 关闭Dialog
    exit() {
      this.dialogFormVisible = false;
      this.clearDialog();
    },
    // 关键字搜索  搜索按钮  以及input改变事件
    async changeKeyWords() {
      await this.getListArticles();
      this.articlesList = this.articlesList.filter(item =>
        item.title.includes(this.keywords) ? item : null
      );
    },
    // 情况搜索
    async clearKeyWords() {
      this.keywords = null;
      await this.getListArticles();
    },
    // 预览item
    Preview(item) {
      this.articlesItem = item;
      this.dialogFormVisiblePreview = true;
      console.log(item);
    }
  },
  created() {
    this.getListArticles();
  },
  filters: {
    states: function(val) {
      if (val === 1) {
        return "启用";
      } else if (val === 0) {
        return "禁用";
      }
    }
  }
};
</script>

<style scoped>
</style>
