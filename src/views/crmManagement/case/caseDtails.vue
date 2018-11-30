<template>
  <div class="x_caseDtails">
    <el-card style="margin-bottom: 15px;">
      <div class="x_case_name">{{date.name}}</div>
      <el-rate :value="date.designer" disabled :max="date.designer"></el-rate>
      <div class="x_case_edit" v-if="date.can_op">编辑案例<i class="el-icon-edit" @click="dialogOpen" style="display: inline-block;"></i></div>
      <div style="clear: both"></div>
      <p class="case_p_width">
        <span>分类产品：{{date.product}}</span>
        <span>客户:{{date.client}}</span>
      </p>
      <p class="case_p_width">
        <span>行业：{{date.industry}}</span>
        <span>地区：{{date.address}}</span>
      </p>
      <p class="case_p_width">
        <span>设计师：{{date.producer}}</span>
        <span>是否可外宣：{{date.can_publicity}}</span>
      </p>
      <p class="case_p_width">
        <span>浏览量：{{date.view}}</span>
      </p>
      <p class="x_lable" style="width: 94px;margin-right: 5px;">{{date.make_time}}</p>
      <p class="x_lable">{{date.lable.name}}</p>
      <div class="x_case_download">
        <span class="tpr">
        <span class="x_lable x_lable_img" :class="{x_lable_img1:date.is_praise}" style="width:80px;border:none;" @click="pointPraise"><b>{{date.praise}}</b></span>
        </span>
      </div>
      <div class="clearfix"></div>
    </el-card>
    <el-card style="float: left;width: 70%;margin-right: 15px;">
      <div class="x_case_name1">创意思路</div>
      <p class="date_edit">{{date.idea}}</p>
      <p v-html="date.edit" class="date_edit_img"></p>
    </el-card>
    <el-card>
      <p class="x_case_name1" style="border-bottom: 1px solid #ebeef5; margin-bottom: 15px;">封面</p>
      <p class="date_cover_img">
        <img :src="over" alt=""  title="点击查看大图"  @click="eImgClick($event)" >
      </p>
      <transition name="ts">
        <imgview v-if="isImgViewsShow" :src="imgViewsSrc" @click="eImgViewClick"></imgview>
      </transition>
      <p class="date_brief">{{date.brief}} </p>
      <p> 附件:</p>
      <el-table :data="date.annex" align="center" style="width: 100%">
        <el-table-column
          prop="name"
          label="名称"
          align="center">
        </el-table-column>
        <el-table-column
          prop="size"
          label="大小"
          align="center">
        </el-table-column>
        <el-table-column
          label="操作"
          align="center">
          <template slot-scope="scope">
            <el-button  type="text" size="small"  @click="fileClone(scope.$index,date.annex)">下载</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <!--案例修改-->
    <el-dialog title="案例详情修改" :visible.sync="dialogTableVisible" :modal="false">
      <el-form ref="form" :model="form"  label-position="top" label-width="100px">
        <el-form-item label="案例名称" prop="name" class="x_case_width">
          <el-input v-model="form.name" placeholder="请输入（30个字以内）"  class="x_case_search"></el-input>
        </el-form-item>
        <el-form-item label="客户名称" prop="client" class="x_case_width">
          <el-select
              v-model="form.client"
              filterable
              allow-create
              placeholder="请选择"
              class="x_case_search">
            <el-option
                v-for="(item,index) in client"
                :key="index"
                :label="item.company"
                :value="item.company">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="客户城市" prop="address" class="x_case_width">
          <el-select v-model="form.address" placeholder="请选择"  class="x_case_search">
            <el-option
                v-for="item in region"
                :key="item.id"
                :label="item.name"
                :value="item.name">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签" prop="lable" class="x_case_width">
          <el-select v-model="form.lable" :disabled="!can_label" placeholder="请选择"  class="x_case_search">
            <el-option
                v-for="item in label"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="分类产品" prop="pro" class="x_case_width">
          <el-cascader
            class="x_case_search"
            placeholder="请选择"
            :show-all-levels="false"
            :options="product"
            v-model="form.product_id"
            filterable>
          </el-cascader>
        </el-form-item>
        <el-form-item label="出品人星级" prop="designer" class="x_case_width">
          <el-select v-model="form.designer" placeholder="请选择" class="x_case_search">
            <el-option
              v-for="item in producerLevel"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="案例日期" prop="make_time" class="x_case_width">
          <el-date-picker
            style="width:270px;"
            v-model="form.make_time"
            type="date"
            placeholder="选择日期"
            :picker-options="pickerOptions">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="行业" prop="industry" class="x_case_width">
          <el-select v-model="form.industry" placeholder="请选择" filterable class="x_case_search">
            <el-option
              v-for="item in industry"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="出品人" prop="producer" class="x_case_width">
          <el-select v-model="form.producer"  placeholder="请选择" multiple filterable allow-create class="x_case_search">
            <el-option
              v-for="(item, index) in person"
              :key="index"
              :label="item.nickname"
              :value="item.nickname">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="浏览量" prop="view" class="x_case_width">
          <el-input v-model="form.view" placeholder="请输入浏览量"  class="x_case_search"></el-input>
        </el-form-item>
        <el-form-item label="是否可外宣" prop="can_publicity" class="x_case_width">
          <el-switch v-model="form.can_publicity"></el-switch>
        </el-form-item>
        <el-form-item label="案例介绍" prop="brief"  style="clear: both">
          <el-input type="textarea" v-model="form.brief" :autosize="{minRows: 4}" placeholder="请输入"></el-input>
        </el-form-item>
        <el-form-item label="创意思路" prop="idea"  style="clear: both">
          <el-input type="textarea" v-model="form.idea" :autosize="{minRows: 4}" placeholder="请输入"></el-input>
        </el-form-item>
        <el-form-item label="封面图" prop="cover"  class="x_case_width" style="position: relative;">
          <img :src="form.cover" alt="" style="float: left;width:100px;height:100px;margin-right: 5px;">
          <casefile  @fileStatus1="fileStatus1"  @fileTrue="fileTrue" :successes="successes" @filexx="filexx"></casefile>
        </el-form-item>
        <div style="clear:both;"></div>
        <el-form-item label="源文件">
          <upload @fileStatus="fileStatus" :success="success" @fileTrue="fileTrue"></upload>
          <el-table border :data="attachment" style="width:80%;margin-top: 10px;">
            <el-table-column label="文件名" prop="name">
            </el-table-column>
            <el-table-column prop="size" label="大小">
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click.native.prevent="deleteRow(scope.$index,attachment)" type="text" size="small">移除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-form-item>
        <div class="clearfix"></div>
        <editorUpload @editup="editup" :xkedit="xkedit" :successful="successful"></editorUpload>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="confirmCancel()">取 消</el-button>
        <el-button type="primary" @click="confirmAdd(form)">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import upload from '../.././components/s_customer/fiieDataCe'
  import  casefile from '../.././components/x_case/case_file'
  import  editorUpload from '../.././components/x_case/editorUpload'
  import  imgview from  '../.././components/x_case/imgview'
  import { quillEditor } from 'vue-quill-editor'
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'
  export default {
    name: 'casedtails',
    components: {
      upload,
      casefile,
      quillEditor,
      editorUpload,
      imgview
    },
    data(){
      return{
        options5: [{
          value: '111',
          label: 'HTML'
        }, {
          value: 'CSS',
          label: 'CSS'
        }, {
          value: 'JavaScript',
          label: 'JavaScript'
        }],
        isImgViewsShow:0,
        cover_source:'',
        imgViewsSrc:'',
        dialogTableVisible:false,
        successful:false,
        successes:false,
        success:false,
        id:'',
        xkedit:'',
        date:{
          id:'',
          name:'',
          client:'',
          industry:'',
          address:'',
          producer:'',
          can_publicity:'',
          lable:[],
          designer:1,
          make_time:'',
          praise:'',
          idea:'',
          edit:'',
          cover:'',
          brief:'',
          can_op:'',
          product:'',
          annex:[],
          view: '',
        },
        //编辑案例页面
        client:[],
        industry:[],
        label:[],
        producerLevel:[],
        product:[],
        region:[],
        attachment:[],
        can_label:false, //能否编辑标签的权限
        form:{
          name:'',            // 案例
          client: '',         // 客户
          address:'',         // 客户地址
          lable:'',           // 标签
          industry: '',       // 行业
          product_id:[],      // 产品id
          make_time:'',       // 时间
          designer:'',        // 出品人星级
          brief:'',           // 案例介绍
          idea: '',           // 创意思路
          edit: '',           // 内容预览
          can_publicity:false,// 外宣
          file:[],
          cover_source:'',
          view: ''
        },
        pickerOptions: {
          disabledDate(time) {
            return time.getTime() > Date.now();
          },
          shortcuts: [{
            text: '今天',
            onClick(picker) {
              picker.$emit('pick', new Date());
            }
          }, {
            text: '昨天',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit('pick', date);
            }
          }, {
            text: '一周前',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', date);
            }
          }]
        },
      }
    },
    computed:{
      over(){
        let ossUrl = this.date.cover.indexOf('?');
        return this.date.cover.substring(0,ossUrl);
      },
      person () {
        return this.$store.state.app.commonPerson;
      }
    },
    methods:{
      //oss
      filexx(data) {
        //封面图上传后随后点击删除 需要让封面图展示默认封面图
        if(data) {
          this.form.cover_source = this.cover_source;
        }
      },
      editup(data){
        this.form.edit = data;
      },
      fileStatus(data) {
        this.form.file = data;
      },
      fileStatus1(data) {
        this.form.cover_source = data[0].path;
      },
      fileTrue(data){
        this.success = data;
      },
      pointPraise(){
        if(!this.date.is_praise){
          this.$put('case/praise/' + this.id)
            .then((data) => {
              if(data.code){
                this.$message({
                  message:data.errorMsg,
                  type: 'success',
                });
                this.caseDtail();
              }else{
                this.$message.error(data.errorMsg);
              }
            })
            .catch(() => {
              this.$message.error('服务器错误，请稍后重试');
            })
        }else {
          this.$message({
            message: '您已经点过赞了',
            type: 'warning'
          });
        }
      },
      fileClone(index, rows){
        window.open(rows[index].path);
      },
      dialogOpen() {
        this.dialogTableVisible = true;
        this.$get('case/editPageData/' + this.$route.params.id)
          .then((data) => {
            if(data.code) {
              this.industry = data.content.industry;
              // this.producer = data.content.producer;
              this.label = data.content.label;
              this.producerLevel = data.content.producerLevel;
              this.product = data.content.product;
              this.region = data.content.region;
              this.attachment = data.content.case.attachment;
              let form = data.content.case;
              // 默认值
              this.form.name = form.name;
              this.form.product_id = form.product_id;
              // this.form.product_id = ["1","4","1"];
              this.form.client = form.client.id;
              /*let client = form.client.id;
              let cli = parseInt(client);
              this.form.client = isNaN(cli) ? form.client.id : client;
              console.log(this.form.client);*/
              this.form.industry = form.industry.id;
              this.form.address = form.address;
              this.form.producer = form.producer;
              this.form.can_publicity = form.can_publicity;
              this.form.lable = form.lable.id;
              this.form.designer = form.designer.id;
              this.form.make_time = form.make_time;
              this.form.idea = form.idea;
              this.xkedit = form.edit;
              this.form.cover = form.cover;
              this.cover_source = form.cover_source;
              this.form.brief = form.brief;
              this.form.view = form.view;
              this.can_label = form.can_label;
            }else {
              this.$message.error(data.errorMsg);
              this.$router.push({name:"caseList"});
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      deleteRow(index, rows){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$delete( 'case/attachment/' + rows[index].id)
            .then( (data) => {
              if(data.code) {
                this.$message({
                  message: data.errorMsg,
                  type: 'success'
                });
                rows.splice(index, 1);
              }
            })
            .catch(() => {
              this.$message.error('服务器错误，请稍后重试');
            })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
      confirmCancel(){
        this.dialogTableVisible = false;
        this.caseDtail();
      },
      confirmAdd(){
        if(!this.form.edit){
          this.form.edit = this.xkedit;
        }
        if(!this.form.brief){
          this.$message({
            message: '请输入案例介绍',
            type: 'warning'
          });
        }else if(!this.form.idea){
          this.$message({
            message: '请输入创意思路',
            type: 'warning'
          });
        }else {
          if(this.form.cover) {
            this.form.cover = '';
          }
          if(!this.form.cover_source) {
            this.form.cover_source = this.cover_source;
          }
          // console.log(this.form);
          this.$put('case/' + this.id,this.form)
            .then((data) => {
              if (data.code) {
                this.$message({
                  message: '修改成功！',
                  type: 'success'
                });
                this.dialogTableVisible = false;
                this.success = true;
                this.successes = true;
                this.successful = true;
                this.form.cover_source = '';
                // this.$refs[formName].resetFields();
                // console.log(this.form);
                this.caseDtail();
              } else {
                this.$message.error(data.errorMsg);
              }
            })
            .catch(() => {
              this.$message.error('服务器错误，请稍后重试');
            })
        }
      },
      caseDtail(){
        this.$get('case/' + this.$route.params.id)
          .then((data) => {
            if(data.code) {
              console.log(data);
              this.id = data.content.id;
              this.date.name = data.content.name;
              this.date.client = data.content.client;
              this.date.industry = data.content.industry;
              this.date.address = data.content.address;
              this.date.producer = data.content.producer;
              this.date.can_publicity = data.content.can_publicity;
              this.date.lable = data.content.lable;
              this.date.designer = data.content.designer.id + 1;
              this.date.make_time = data.content.make_time;
              this.date.praise = data.content.praise;
              this.date.idea = data.content.idea;
              this.date.edit = data.content.edit;
              this.date.cover = data.content.cover;
              this.date.brief = data.content.brief;
              this.date.is_praise = data.content.is_praise;
              this.date.can_op = data.content.can_op;
              this.date.product = data.content.product;
              this.date.annex = data.content.annex;
              this.date.view = data.content.view;
            }else {
              this.$message.error(data.errorMsg);
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      //点击图片放大
      eImgClick(e){
        this.isImgViewsShow = 1;
        this.imgViewsSrc = e.currentTarget.src
      },
      eImgViewClick(){
        this.isImgViewsShow = 0;
      },
      getClient() {
        this.$get('case/allClient')
          .then((data) => {
            if(data.code === 200) {
              this.client = data.content;
            }
          })
          .catch(() => {

          })
      }
    },
    created(){
      this.caseDtail();
      this.getClient();
    }
  }
</script>

<style lang="less">
  .x_caseDtails{

    .x_case_name{
      height: 50px;
      font-size: 16px;
      float: left;
      text-align: left;
      font-weight: 700;
      margin-right: 20px;
    }
    .x_case_name1{
      height: 50px;
      font-size: 16px;
      font-weight: 700;
    }
    .date_edit{
      width: 100%;
      border-bottom: 1px solid #ebeef5;
      margin-bottom: 15px;
      font-size: 14px;
      box-sizing: border-box;
      padding: 0 20px;
      word-wrap:break-word;
      word-break:break-all;
      overflow: hidden;
    }
    .date_edit_img  img{
      margin:0 auto;
      width:auto;
      height:auto;
      max-width:100%;
      max-height:100%;
    }
    .date_edit_img p{
      line-height: 30px;
      font-size: 14px;
    }
    .x_case_width{
      width: 33%;
      float: left;
    }
    .x_case_search{
      width: 270px;
      height: 50px;
    }
    .date_cover{
      width: 100%;
      height: 500px;
    }
    .date_cover_img{
      width:100%;
      height:186px;
      max-width:100%;
      max-height:100%;
    }
    .date_cover_img img{
      width:auto;
      height:186px;
      display: block;
      max-width:100%;
      max-height:100%;
      margin: 0 auto;
    }
    .date_cover_img:hover{
      cursor: pointer;
    }
    .clearfix:before,
    .clearfix:after {
      display: table;
      content: "";
    }
    .x_lable_img:hover{
      cursor: pointer;
    }
    .date_brief{
      width: 100%;
      border-bottom: 1px solid #ebeef5;
      margin:15px 0px;
      line-height: 30px;
      text-indent:20px;
      word-wrap:break-word;
      word-break:break-all;
      overflow: hidden;
    }
    .clearfix:after {
      clear: both
    }
    .x_case_edit{
      float: right;
      width:100px;
      font-size: 14px;
      margin-right: 30px;
    }
    .x_case_download{
      float: right;
      margin-right: 60px;
    }
    .case_p_width{
      line-height: 30px;
    }
    .case_p_width span{
      display: inline-block;
      width: 35%;
      font-size: 14px;
    }
    .quill-editor {
      height: 380px;
      clear: both;

      .ql-container {
        height: 280px;
      }
    }
    .limit {
      height: 30px;
      border: 1px solid #ccc;
      line-height: 30px;
      text-align: right;

      span {
        color: #ee2a7b;
      }
    }
    .ql-snow .ql-editor img {
      max-width: 80px;
    }
    .ql-editor .ql-video {
      max-width: 80px;
    }
    .x_lable{
      display: inline-block;
      text-align: center;
      width: 50px;
      height: 24px;
      border-radius: 15px;
      border: 1px solid #99d4ff;
      font-size: 12px;
      line-height: 24px;
      color:#99d4ff;
    }
    .tpr{
      display: inline-block;
      text-align: center;
      width: 80px;
      height: 24px;
      border-radius: 15px;
      font-size: 12px;
      line-height: 24px;
      background: #f8f8f8;
    }
    .x_lable_img{
      background: url("../../.././images/parse.png") no-repeat;
      background-position: 18px 5px;
    }
    .x_lable_img1{
      background: url("../../.././images/parses.png") no-repeat;
      background-position: 18px 5px;
    }
    .x_lable_img b{
      margin-left: 35px;
      color:#ccc;
    }
    .x_lable_img1 b{
      color:#f92d54;
    }
    .el-icon-edit:hover{
      cursor: pointer;
    }
    ts-enter-active,
    ts-leave-active{
      transition:all .5s;
    }
    ts-enter,
    ts-leave-active{
      transform: translate(100%,0);
    }
    @media screen and (max-width: 1366px) {
      .el-dialog{
        width: 68%;
      }
    }
  }
</style>
