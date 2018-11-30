<template>
  <div class="addCase">
    <el-card>
      <div class="x_case_title">
        案例信息
      </div>
      <el-form ref="form" :model="form"  label-position="top" label-width="100px">
        <el-form-item label="案例名称（必填）" prop="name" class="x_case_width">
          <el-input v-model="form.name" placeholder="请输入（30个字以内）"  class="x_case_search"></el-input>
        </el-form-item>
        <el-form-item label="客户名称（必填）" prop="client" class="x_case_width">
          <el-select v-model="form.client"  filterable allow-create placeholder="请选择" class="x_case_search">
            <el-option
                v-for="item in client"
                :key="item.id"
                :label="item.company"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="客户城市（必填）" filterable prop="address" class="x_case_width">
          <el-select v-model="form.address" placeholder="请选择" class="x_case_search">
            <el-option
                v-for="item in region"
                :key="item.id"
                :label="item.name"
                :value="item.name">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签（必填）" prop="lable" class="x_case_width">
          <el-select v-model="form.lable" placeholder="请选择" disabled  class="x_case_search">
            <el-option
                v-for="item in label"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="分类产品（必填）" prop="product_id" class="x_case_width">
          <el-cascader
              class="x_case_search"
              placeholder="请选择"
              :show-all-levels="false"
              :options="product"
              v-model="form.product_id"
              filterable
          ></el-cascader>
        </el-form-item>
        <el-form-item label="出品人星级（必填）" prop="designer" class="x_case_width">
          <el-select v-model="form.designer" placeholder="请选择" class="x_case_search">
            <el-option
                v-for="item in producerLevel"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="案例日期（必填）" prop="make_time" class="x_case_width">
          <el-date-picker
              style="width: 90%;"
              v-model="form.make_time"
              type="date"
              placeholder="选择日期"
              :picker-options="pickerOptions">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="行业（必填）" prop="industry" class="x_case_width">
          <el-select v-model="form.industry" placeholder="请选择" filterable class="x_case_search">
            <el-option
                v-for="item in industry"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="出品人（必填）" prop="producer" class="x_case_width">
          <el-select v-model="form.producer" multiple placeholder="请选择" filterable allow-create class="x_case_search">
            <el-option
                v-for="item in person"
                :key="item.dd_id"
                :label="item.nickname"
                :value="item.nickname">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="浏览量" prop="view" class="x_case_width">
          <el-input v-model="form.view" placeholder="请输入浏览次数"  class="x_case_search"></el-input>
        </el-form-item>
        <el-form-item label="是否可外宣" prop="can_publicity" class="x_case_width">
          <el-switch v-model="form.can_publicity" active-value=1 inactive-value=0></el-switch>
        </el-form-item>
        <el-form-item prop="brief" label="案例介绍（必填）"  style="clear: both;">
          <el-input type="textarea" v-model="form.brief" :autosize="{ minRows: 3}" placeholder="请输入" style="width: 40%;"></el-input>
        </el-form-item>
        <el-form-item prop="idea" label="创意思路（必填）"  style="clear: both">
          <el-input type="textarea" v-model="form.idea" :autosize="{ minRows: 3}" placeholder="请输入" style="width: 40%;"></el-input>
        </el-form-item>
        <el-form-item  label="封面图（必填）"  class="x_case_width">
          <casefile @fileStatus1="fileStatus1"  @fileTrue1="fileTrue1" :successes="successes"></casefile>
        </el-form-item>
        <el-form-item label="源文件（必填）"  class="x_case_width" style="margin-left: 20px;" >
          <upload @fileStatus="fileStatus" :success="success" @fileTrue="fileTrue"></upload>
        </el-form-item>
        <addEditorUpload @editup="editup" :successful="successful" @fileTrue2="fileTrue2" style="clear:both;margin-top: 180px;"></addEditorUpload>
        <el-form-item>
          <el-button type="primary" @click="submitForm('form')">立即创建</el-button>
          <el-button @click="resetForm('form')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
  import upload from '../.././components/s_customer/fiieDataCe'
  import casefile from '../.././components/x_case/case_file'
  import { quillEditor } from 'vue-quill-editor'
  import addEditorUpload from '../.././components/x_case/addEditorUpload'
  export default {
    components: {
      upload,
      casefile,
      quillEditor,
      addEditorUpload
    },
    data () {
      return {
        success: false,  //传文件
        successes:false, //封面图
        successful:false, //改造后的富文本编辑器 的清空默认值
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
        form: {
          name:'',            // 案例
          client: '',         // 客户
          address:'',         // 客户地址
          lable:0,           // 标签
          industry: '',       // 行业
          product_id:[],      // 产品id
          make_time:'',       // 时间
          designer:'',        // 出品人星级
          brief:'',           // 案例介绍
          idea: '',           // 创意思路
          producer:[],        // 出品人
          edit: '',           // 内容预览
          can_publicity:0,    // 外宣
          file:[],
          cover:'',
          view: ''
        },
        cover:'',
        client:[],
        industry:[],
        producer:[],
        label:[],
        producerLevel:[],
        product:[],
        region:{},
      }
    },
    computed: {
      person () {
        return this.$store.state.app.commonPerson;
      }
    },
    methods: {
      //oss
      editup(data){
        this.form.edit = data;
      },
      fileStatus(data) {
        this.form.file = data;
      },
      fileStatus1(data) {
        this.form.cover = data[0].path;
      },
      fileTrue(data){
        this.success = data;
      },
      fileTrue1(data){
        this.successes = data;
      },
      fileTrue2(data){
        this.successful = data;
      },
      addCase(){
        this.$get('/case/addPageData')
          .then((data) => {
            if(data.code){
              // this.client = data.content.client;
              this.industry = data.content.industry;
              // this.producer = data.content.producer;
              this.label = data.content.label;
              this.producerLevel = data.content.producerLevel;
              this.product = data.content.product;
              this.region = data.content.region;
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      submitForm(formName) {
        if(!this.form.name){
          this.$message({
            message: '请填写案例名称',
            type: 'warning'
          });
        }else if(!this.form.client){
          this.$message({
            message: '请选择客户名称',
            type: 'warning'
          });
        }else if(!this.form.address){
          this.$message({
            message: '请选择客户城市',
            type: 'warning'
          });
        }else if(this.form.lable.length == 0){
          this.$message({
            message: '请选择标签',
            type: 'warning'
          });
        }else if(this.form.product_id.length < 1){
          this.$message({
            message: '请选择分类产品',
            type: 'warning'
          });
        }else if(this.form.designer != '0' && this.form.designer === ''){
          this.$message({
            message: '请选择出品人星级',
            type: 'warning'
          });
        }else if(!this.form.make_time){
          this.$message({
            message: '请选择案例日期',
            type: 'warning'
          });
        }else if(!this.form.industry){
          this.$message({
            message: '请选择行业',
            type: 'warning'
          });
        }else if(this.form.producer.length < 1){
          this.$message({
            message: '请选择出品人',
            type: 'warning'
          });
        }else if(!this.form.brief){
          this.$message({
            message: '请输入案例介绍',
            type: 'warning'
          });
        }else if(!this.form.idea){
          this.$message({
            message: '请输入创意思路',
            type: 'warning'
          });
        }else if(!this.form.cover){
          this.$message({
            message: '请选择封面图',
            type: 'warning'
          });
        }else if(this.form.file.length < 1){
          this.$message({
            message: '请选择源文件',
            type: 'warning'
          });
        }else if(!this.form.edit){
          this.$message({
            message: '请填写内容预览',
            type: 'warning'
          });
        }else{
          this.$post('case',this.form)
            .then((data) => {
              if(data.code) {
                this.$message({
                  message: data.errorMsg,
                  type: 'success'
                });
                this.success = true;
                this.successes = true;
                this.successful = true;
                this.form.file = [];
                this.form.edit = '';
                this.form.cover = '';
                this.$refs[formName].resetFields();
                this.$router.push({
                  name: 'caseDetail',
                  params: {
                    id: data.content.id
                  }
                })
              } else {
                this.$message({
                  message: data.errorMsg,
                  type: 'warning'
                });
              }
            })
            .catch(() => {
              this.$message.error('服务器错误，请稍后重试');
            })
        }
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
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
      this.addCase();
      this.getClient();
    }
  }
</script>

<style lang="less">
  .addCase{
    .x_case_title{
      width: 100%;
      height: 50px;
      font-size: 16px;
      float: left;
      text-align: left;
      font-weight: 700;
    }
    .x_case_width{
      width: 33%;
      float: left;
    }
    .x_case_search{
      width: 90%;
      height: 50px;
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
  }
</style>
