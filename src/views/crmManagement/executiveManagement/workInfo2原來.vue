<template>
  <div class="workInfo">
    <div class="workInfo_top">
      <div class="workNumber">
        <span class="NumberIcon"><img src="../../../../src/images/icon.png"></span>
        <span class="number">单号：{{ message.number }}</span>
      </div>
      <div class="workDetails">
        <div class="workDetailsContent">

          <div class="workDetailsLeft">
            <el-row class="detailsMessageFirst">
              <el-col :span="9">创建人：<span class="detailsMessageText">{{ message.person }}</span></el-col>
              <el-col :span="9">产品： <span class="detailsMessageText">{{ message.product }}</span></el-col>
            </el-row>
            <el-row class="detailsMessage">
              <el-col :span="9">工单类型：<span class="detailsMessageText">{{ message.link_type }}</span></el-col>
              <el-col show-overflow-tooltip="true" :span="11">来源单据： <span @click="itemOrderDetail" style="cursor:pointer;color:#38adff">{{ message.code}}</span></el-col>
            </el-row>
            <el-row class="detailsMessage">
              <el-col :span="9">客户： <span @click="clientDeatil" style="color:#38adff;cursor:pointer">{{ message.client }}</span></el-col>
              <el-col :span="11" v-if="task_type == 1">预计调用日期：<span class="detailsMessageText">{{ message.expect_time }}</span></el-col>
              <el-col :span="11" v-if="task_type == 2">合同是否签订：<span class="detailsMessageText">{{ message.contract ? '是' : '否' }}</span></el-col>
            </el-row>
            <el-row class="detailsMessage">
              <el-col :span="11" v-if="task_type == 1">调用类型： <span @click="clientDeatil" class="detailsMessageText">{{ message.call_that }}</span></el-col>
            </el-row>
            <el-row class="detailsMessage" v-if="task_type == 1">
              提案结果：{{ message.result }}
            </el-row>
          </div>

          <div class="workDetailsRight">
            <el-col v-for="item in menu" :key="item.index">
              <el-button class='workDetailsButton' style="background-color: #ff2e56;color: #FFFFFF"  @click="workEnd" v-if="item.end">{{ item.end }}</el-button>
              <el-button class='workDetailsButton' type="primary" @click="workReject" v-if="item.reject">{{ item.reject }}</el-button>
              <el-button class='workDetailsButton' type="primary" @click="show" v-if="item.input_content">{{ item.input_content }}</el-button>
              <el-button class='workDetailsButton' type="primary" @click="determine" v-if="item.Determine">{{ item.Determine }}</el-button>
            </el-col>
          </div>
          
        </div>
        <div class="workDetailsBottom">
          <div class="workDetailsBottomMessage" v-if="task_type == 2">已回款金额：<span class="detailsMessageBottomText">{{ message.sum_price}}</span></div>
          <div class="workDetailsBottomMessage" v-if="task_type == 2">已托管金额：<span class="detailsMessageBottomText">{{ message.trusteeship_price}}</span></div>
          <div class="workDetailsBottomMessage">金额：<span class="detailsMessageBottomText">{{ message.price }}</span></div>
          <div class="workDetailsBottomMessage">状态：<span class="detailsMessageBottomText">{{ message.status}}</span></div>
        </div>
      </div>
    </div>
    <div class="s_workSummary">
      <el-collapse>
        <el-collapse-item v-if="message.summary">
          <template slot="title">
            提案总结：{{ message.summary }}
          </template>
          <div style="color: #999;">{{ message.summary }}</div>
        </el-collapse-item>
        <el-collapse-item>
          <template slot="title">
            申请说明：{{ message.description }}
          </template>
          <div style="color: #999;">{{ message.description }}</div>
        </el-collapse-item>
        <el-collapse-item>
          <template slot="title">
            分配说明：{{ message.remarks}}
          </template>
          <div style="color: #999;">{{ message.remarks }}</div>
        </el-collapse-item>
      </el-collapse>
    </div>
    <div class="workInfo_content">
      <el-tabs value="first">
        <el-tab-pane v-model="tabActiveName" label="详情" name="first">
          <div class="progress_bar">
            <div class="first_style_title">
              <img src="../../../../src/images/jindu.png"><span class="workInfo_content_title">流程进度</span>
            </div>
            <div class="rocket">
              <div v-for="(item,index) in number" :key="item.id">
                <div :class="'rocket_style' +  index " v-if="number == item">
                  <img class="s_workIfo_img" src="../../../../src/images/rocket.jpg">
                </div>
              </div>
            </div>
            <div class="first_style_content">
              <el-steps :active="number">
                <el-step
                    v-for="(item, index) in process"
                    v-bind:key="item.index"
                    :class="'s_workinfo_step_' + index"
                    :title="item.title"
                    :description="item.content"
                >
                </el-step>
              </el-steps>
            </div>
          </div>
          <div class="implement">
            <div class="first_style_title">
              <img src="../../../../src/images/renyuan.png"><span class="workInfo_content_title">执行人员</span>
              <el-button v-if="add_excutor" @click="addExecutor" size="mini" plain round type="primary" class="s_workInfoBtn">新增执行人</el-button>
            </div>
            <el-table :data="executorData" border style="width: 100%;">
              <el-table-column fixed prop="nickname" label="姓名"></el-table-column>
              <el-table-column prop="position" label="岗位"></el-table-column>
              <el-table-column prop="department" label="部门"></el-table-column>
              <el-table-column prop="create_nickname" label="指派人"></el-table-column>
              <el-table-column prop="remarks" label="备注"></el-table-column>
              <el-table-column prop="result" label="取消原因"></el-table-column>
              <el-table-column prop="is_hidden" label="状态"></el-table-column>
              <el-table-column fixed="right" label="操作">
                <template slot-scope="scope">
                  <el-button v-if="add_excutor" @click="handleClick(scope.$index, executorData)" type="text" size="small">{{ scope.row.operation }}</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="tast">
            <div class="first_style_title">
              <img src="../../../../src/images/mingxi.png"><span class="workInfo_content_title">Brief明细</span>
              <el-button v-if="add_brife" type="primary" @click="addTask" plain round size="mini" class="s_workInfoBtn">新增Brief</el-button>
              <span style="float: right;">初稿：{{ first_count }}，    修改： {{ second_count }}</span>
            </div>
            <el-table :data="breifDetail" border style="width: 100%">
              <el-table-column fixed prop="task_number" label="Brief编号"></el-table-column>
              <el-table-column prop="title" label="Brief标题"></el-table-column>
              <el-table-column prop="task_status" label="类型">
                <template slot-scope="scope">
                  <span v-if="scope.row.task_status =='1'">初稿</span>
                  <span v-else>修改</span>
                </template>
              </el-table-column>
              <el-table-column prop="pro_name" label="产品"></el-table-column>
              <el-table-column prop="man" label="执行人"></el-table-column>
              <el-table-column prop="status_title" label="状态"></el-table-column>
              <el-table-column prop="delay" label="已延期人员"></el-table-column>
              <el-table-column prop="end_time" label="要求截止时间"></el-table-column>
              <el-table-column prop="delay" label="操作">
                <template slot-scope="scope">
                  <el-button @click.native.prevent="taskDetail(scope.$index, breifDetail)" type="text" size="small">查看</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-tab-pane>
        <el-tab-pane label="附件" name="second">
          <el-card>
            <div><el-button type="primary" round @click="newFlie" size="small" plain style="margin-left: 10px;">新增附件</el-button></div>
            <el-table :data="annexData" style="width: 100%">
              <el-table-column fixed prop="name" label="附件名称"></el-table-column>
              <el-table-column prop="person" label="创建人"></el-table-column>
              <el-table-column prop="size" label="大小"></el-table-column>
              <el-table-column prop="brife" label="备注"></el-table-column>
              <el-table-column prop="create_time" label="创建时间"></el-table-column>
              <el-table-column fixed="right" label="操作">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="fileUpload(scope.$index, annexData)">
                    下载
                  </el-button>
                  <el-button :disabled="(!scope.row.delete)" type="text" size="small" @click.native.prevent="deleteRow(scope.$index, annexData)">
                    移除
                  </el-button>
                </template>
              </el-table-column>
            </el-table>
            <el-dialog title="新增附件" :visible.sync="newFileAdd" :modal="false">
              <upload @fileStatus="fileStatus" @fileTrue="fileTrue" :success="success"></upload>
              <el-row style="margin-top: 20px;">
                <el-col :span="2">
                  备注
                </el-col>
                <el-col>
                  <el-input type="textarea" :rows="5" v-model="brife" placeholder="请输入内容"></el-input>
                </el-col>
              </el-row>
              <span slot="footer" class="dialog-footer">
                        <el-button @click="newFileAdd = false">取 消</el-button>
                        <el-button type="primary" @click="newFileAddT">确 定</el-button>
                    </span>
            </el-dialog>
          </el-card>
        </el-tab-pane>
        <el-tab-pane label="操作记录" name="third">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>操作日志</span>
            </div>
            <el-table :data="operateLog">
              <el-table-column prop="nickname" label="人员" width="150"></el-table-column>
              <el-table-column prop="content" label="操作内容"></el-table-column>
              <el-table-column prop="create_time" label="操作时间"></el-table-column>
            </el-table>
          </el-card>
        </el-tab-pane>
      </el-tabs>
    </div>
    <el-dialog title="" :visible.sync="centerDialogVisible" width="50%" center :modal="false">
            <span v-for="item in input_content" :key="item.id">
                <el-row v-if="item.type === 'choose'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-switch v-model="r_data[item.name]" @change></el-switch>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'pay_choose'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-switch v-model="r_data.first_pay" @change></el-switch>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'pay_time'" v-show="r_data.first_pay">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-date-picker
                            v-model="r_data[item.name]"
                            type="date"
                            placeholder="选择日期"
                            format="yyyy 年 MM 月 dd 日"
                            value-format="yyyy-MM-dd">
                        </el-date-picker>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'pay_select'" v-show="r_data.first_pay">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-select v-model="r_data[item.name]" filterable placeholder="请选择">
                            <el-option
                                v-for="val in item.content"
                                :key="val.value"
                                :label="val.label"
                                :value="val.value">
                            </el-option>
                        </el-select>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'pay_input'" v-show="r_data.first_pay">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-input v-model="r_data[item.name]" type="number" placeholder="￥(元)" style="width:220px"></el-input>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'time'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-date-picker
                            v-model="r_data[item.name]"
                            type="date"
                            placeholder="选择日期"
                            format="yyyy 年 MM 月 dd 日"
                            value-format="yyyy-MM-dd">
                        </el-date-picker>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'select'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-select v-model="r_data[item.name]" filterable clearable placeholder="请选择">
                            <el-option
                                v-for="val in item.content"
                                :key="val.value"
                                :label="val.label"
                                :value="val.value">
                            </el-option>
                        </el-select>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'option'">
                  <el-col :span="4" style="line-height: 40px">
                        {{item.title}}
                    </el-col>
                  <el-col :span="8">
                    <el-select v-model="profile" @change="handleCompany" placeholder="请选择" filterable clearable>
                      <el-option
                          v-for="item in company_info"
                          :key="item.company_id"
                          :label="item.name"
                          :value="item.company_id">
                      </el-option>
                    </el-select>
                  </el-col>
                    <el-col :span="8">
                        <el-select v-model="r_data[item.name]" multiple filterable placeholder="请选择">
                            <el-option
                              v-for="(item, index) in person"
                              :key="index"
                              :label="item.nickname"
                              :value="item.dd_id">
                            </el-option>
                          </el-select>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'provider'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-select v-model="r_data[item.name]" clearable filterable allow-create placeholder="请选择服务商">
                            <el-option
                                v-for="provider in item.content"
                                :key="provider.value"
                                :label="provider.label"
                                :value="provider.value">
                            </el-option>
                        </el-select>
                    </el-col>
                </el-row>
                 <el-row v-if="item.type === 'summary_textarea'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-input type="textarea" :rows="2" v-model="r_data[item.name]" placeholder="请输入内容"></el-input>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'textarea'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-input type="textarea" :rows="2" v-model="r_data[item.name]" placeholder="请输入说明"></el-input>
                    </el-col>
                </el-row>
                 <el-row v-if="item.type === 'file'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <upload @fileStatus="fileStatus" @fileTrue="fileTrue" :success="success"></upload>
                    </el-col>
                </el-row>
                <el-row v-if="item.type === 'file_textarea'">
                    <el-col :span="4">
                        {{item.title}}
                    </el-col>
                    <el-col :span="10">
                        <el-input type="textarea" :rows="2" v-model="r_data[item.name]" placeholder="请输入内容"></el-input>
                    </el-col>
                </el-row>
                <br/>
            </span>
      <span slot="footer" class="dialog-footer">
                <el-button @click="centerDialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="centerDialogVisible_btn">确 定</el-button>
            </span>
    </el-dialog>
    <el-dialog
        :modal="false"
        title="新增执行人"
        :visible.sync="addExecutorDialog">
      <el-row v-if="BasicData.type == 'select'">
        <el-col :span="4">
          {{ BasicData.title }}
        </el-col>
        <el-col :span="10">
          <el-select v-model="r_form.value" filterable placeholder="请选择">
            <el-option
                v-for="index in BasicData.content"
                :key="index.value"
                :label="index.label"
                :value="index.value">
            </el-option>
          </el-select>
        </el-col>
      </el-row>
      <br/>
      <el-row v-if="r_form.value === 'self_support'">
        <el-col :span="4">
          执行人
        </el-col>
        <el-col :span="6">
          <el-select v-model="profile" @change="handleCompany" placeholder="请选择" filterable clearable>
            <el-option
                v-for="item in company_info"
                :key="item.company_id"
                :label="item.name"
                :value="item.company_id">
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="10">
          <el-select v-model="r_form.self_support" multiple filterable placeholder="请选择">
            <el-option
              v-for="(item, index) in person"
              :key="index"
              :label="item.nickname"
              :value="item.dd_id">
            </el-option>
          </el-select>
        </el-col>
      </el-row>
      <br/>
      <el-row v-if="r_form.value === 'service_provider'">
        <el-col :span="4">
          服务商
        </el-col>
        <el-col :span="10">
          <el-select v-model="r_form.service_provider" filterable allow-create placeholder="请选择">
            <el-option
                v-for="item in service_provider"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
        </el-col>
      </el-row>
      <br/>
      <el-row v-if="remark.type == 'textarea'">
        <el-col :span="4">
          {{ remark.title }}
        </el-col>
        <el-col :span="10">
          <el-input type="textarea" :rows="5" placeholder="请输入内容" v-model="r_form.remark"></el-input>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
                <el-button @click="addExecutorDialog = false">取 消</el-button>
                <el-button type="primary" @click="addTrue">确 定</el-button>
            </span>
    </el-dialog>
    <el-dialog title="驳回说明：" :visible.sync="workInfoReject" :modal="false">
            <span>
                <el-input
                    style="width: 400px"
                    type="textarea"
                    :rows="5"
                    placeholder="请输入驳回说明"
                    v-model="RejectDescription">
                </el-input>
            </span>
      <span slot="footer" class="dialog-footer">
                <el-button @click="workInfoReject = false">取 消</el-button>
                <el-button type="primary" @click="workInfoRejectT">确 定</el-button>
            </span>
    </el-dialog>
    <el-dialog title="" :visible.sync="workInCancelForm" :modal="false">
      <el-form :model="cancelform" label-width="120px">
        <el-form-item label="取消原因">
          <el-select v-model="cancelform.result" placeholder="请选择">
            <el-option
                v-for="item in reasons"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否取消该执行人未完成的brief">
          <el-switch v-model="cancelform.brife"></el-switch>
        </el-form-item>
        <el-form-item label="备注">
          <el-input  type="textarea" :rows="5" v-model="cancelform.remarks" placeholder="请输入备注"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="workInCancelForm = false">取 消</el-button>
        <el-button type="primary" @click="workInCancelFormT">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="" :visible.sync="workInReplace" :modal="false">
      <el-form :model="replaceform" label-width="120px">
        <el-form-item label="新客服">
          <el-select v-model="replaceform.new_dd_id" filterable placeholder="请选择">
              <el-option
                  v-for="(item,index) in person"
                  :key="index"
                  :label="item.nickname"
                  :value="item.dd_id">
              </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="更换原因">
          <el-select v-model="replaceform.result" placeholder="请选择">
            <el-option
                v-for="item in reasons"
                :key="item.id"
                :label="item.name"
                :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="历史未完成的Brief是否转移">
          <el-switch v-model="replaceform.brife"></el-switch>
        </el-form-item>
        <el-form-item label="备注">
          <el-input  type="textarea" :rows="5" v-model="replaceform.remarks" placeholder="请输入备注"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="workInReplace = false">取 消</el-button>
        <el-button type="primary" @click="workInReplaceT">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import annexList from '../.././components/s_customer/annexList'
  import upload from '../.././components/s_customer/fiieDataCe'
  import Cookies from 'js-cookie'
  export default {

    components: {
      annexList,
      upload
    },

    data () {
      return {
        value:'',
        operateLog: [],                   // 操作日志数组
        instruction: '',
        RejectDescription: '',
        workInfoReject: false,
        annexData: [],
        number: 0,
        message: {},                       // 工单详情数据
        menu: {},                          // 工单的按钮事件
        input_content: [],                 // 工单派单的数据
        process: [],                       // 详情步骤条
        tabActiveName: 'first',
        centerDialogVisible:false,         // 派单loading框
        workInCancelForm: false,
        workInReplace: false,
        reasons: [
          {id: '1', name: '派单不符'},
          {id: '2', name: '客户不满意'},
          {id: '3', name: '离职'},
          {id: '4', name: '其它'},
        ],
        // 取消执行人发送的数据
        cancelform: {
          result: '',                     // 取消原因
          brife: false,                   // 取消执行人未完成的任务
          remarks: '',                    // 取消执行人备注
          id: '',
          type: '',
          manage_id: '',
        },
        // 更换AE发送数据
        replaceform: {
          result: '',                    // 更换原因
          brife: true,                  // 历史未完成的Brief是否转移
          remarks: '',                   // 更换AE备注
          new_dd_id: '',
          id: '',
        },
        r_data:{                           // 派单需要传送后台的数据
          pre_sale_id: [],
          client_director_id: '',
          dd_id: [],
          provider_id: '',
          brife: '',
          file_remarks: '',
          manage_id: 0,
          step: 0,
          task_id: 0,
          result: '',
          summary: '',
          summary_textarea: '',
          file: [],
          contract: false,
          first_pay: false,
          description: '',
        },
        r_form: {
          value: '',
          self_support: [],
          service_provider: '',
          remark: '',
          manage_id:'',
        },
        brife: '',
        add_brife: false,
        add_excutor:false,
        executorData: [{}],
        addExecutorDialog: false,
        BasicData: {},
        self_support: [],
        service_provider: [],
        remark: {},
        breifDetail: [],               // 工单任务
        client_id: 0,                  // 客户id
        second_count: 0,               // 修改次数
        first_count: 0,                // 初稿次数
        task_type: 0,                  // 判断项目或订单
        link_id: 0,                    // 项目或订单id
        newFileAdd: false,
        remarks: '',
        file:'',
        name:'',
        path:'',
        size:'',
        success:false,
        updata: false,
        // person: [],
        company: {
          company_id: '',
          dept: ''
        },
        profile: JSON.parse(Cookies.get('company_id')),
      }
    },
    computed: {
      company_info () {
        return this.$store.state.app.company_info;
      },
      person () {
        return this.$store.state.app.person;
      }
    },
    methods: {
      handleCompany (val) {
        this.company.company_id = val;
        this.$store.dispatch('dept', this.company);
      },
      // 取消执行人
      handleClick(index,rows) {
        if(rows[index].operation == '取消'){
          this.workInCancelForm = true;
          this.cancelform.id = rows[index].id;
          this.cancelform.type = rows[index].type;
        } else if (rows[index].operation == '更换'){
          this.workInReplace = true;
          this.replaceform.id = rows[index].id;
        }
      },
      // 取消执行人
      workInCancelFormT () {
        this.$get( 'manage/executorDelete',this.cancelform)
          .then( (data) => {
            if(data.code){
              this.workInCancelForm = false;
              this.cancelform = {};
              this.workData();
              this.workOperateLog();
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
            } else {
              this.$message.error(data.errorMsg);
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      // 更换AE
      workInReplaceT() {
        this.$post( 'manage/changeAe',this.replaceform)
          .then( (data) => {
            if(data.code){
              this.workInReplace = false;
              this.replaceform = {};
              this.workData();
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.workOperateLog();
            } else {
              this.$message.error(data.errorMsg);
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      fileTrue(data) {
        this.success = data;
      },
      //下载
      fileUpload(index, rows){
        window.open(rows[index].path);
      },
      //oss
      fileStatus(data) {
        this.r_data.file = data;
        this.file = data;
      },
      // 新增附件
      newFlie() {
        this.newFileAdd = true;
      },
      // 删除项目附件
      annex (data) {
        this.$delete( 'manage/deleteAttachment/' + data)
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.fileListAll();
              this.workOperateLog();
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
      },
      // 确定执行人添加
      addTrue() {
        this.$post( 'manage/newExecutorAdd',this.r_form)
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.addExecutorDialog = false;
              this.r_form.self_support = [];
              this.r_data.service_provider = '';
              this.r_data.remark = '';
              this.workData();
              this.workOperateLog();
            } else {
              this.$message.error(data.errorMsg);
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      addExecutor () {
        this.addExecutorDialog = true;
      },
      addTask(){
        this.$router.push({name:'addTask'});
      },
      // 驳回
      workInfoRejectT () {
        this.$get( 'manage/reject',{id: this.$route.query.task_id, manage_id:this.$route.query.manage_id, remarks: this.RejectDescription})
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.$store.commit('removeTag','workInfo');
              this.$store.commit('closePage','workInfo');
              this.$router.push({name: 'workOrderList'});
              this.RejectDescription = '';
              this.workInfoReject = false;
              // this.workOperateLog();
            } else {
              this.$message.error(data.errorMsg);
            }
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      // 工单驳回事件
      workReject() {
        this.workInfoReject = true;
      },
      // 工单终止事件
      workEnd () {
        this.$confirm('请确定终止?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$get( 'manage/end',{ manage_id:this.$route.query.manage_id})
            .then( (data) => {
              if(data.code){
                this.$message({
                  message: data.errorMsg,
                  type: 'success'
                });
                this.$router.go(0);
              } else {
                this.$message.error(data.errorMsg);
              }
            })
            .catch(() => {
              this.$message.error('服务器错误，请稍后重试');
            })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消终止'
          });
        });
      },
      // 显示派单的loading框
      show() {
        this.centerDialogVisible = true;
      },
      // 售前和成单第三步 确定事件
      determine() {
        this.$post( 'manage/post',this.r_data)
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.$router.push({name: 'workOrderList'});
              this.$store.commit('removeTag','workInfo');
              this.$store.commit('closePage','workInfo');
              this.workOperateLog();
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
      },
      // 确定派单事件
      centerDialogVisible_btn(){
        //组件的复用 后台返回的name值会造成干扰；所以后台改了name值 前台需要匹配字段
        if(this.r_data.pre_sale_id7) {
          this.r_data.pre_sale_id = this.r_data.pre_sale_id7;
        }
        if(this.r_data.pre_sale_id2) {
          this.r_data.pre_sale_id = this.r_data.pre_sale_id2;
        }
        if(this.r_data.dd_id3) {
          this.r_data.dd_id = this.r_data.dd_id3;
        }
        if(this.r_data.dd_id8) {
          this.r_data.dd_id = this.r_data.dd_id8;
        }
        this.$post( 'manage/post',this.r_data)
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.centerDialogVisible = false;
              this.r_data = {};
              this.input_content = [];
              this.r_data.pre_sale_id = [];
              this.workOperateLog();
              this.workData();
              this.$router.push({
                name: 'workOrderList'
              });
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
      },
      deleteRow (index, rows){
        this.$delete( 'manage/deleteAttachment/' + rows[index].id)
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.fileListAll();
              this.workOperateLog();
            }
          })
          .catch(() => {
          })
      },
      // 工单详情所需页面
      workData () {
        this.$get( 'manage/get',this.$route.query)
          .then( (data) => {
            if(data.code){
              this.r_data.description = data.content.message.description;
              this.message = data.content.message;
              this.menu = data.content.input.menu;
              this.input_content = data.content.input.input_content;
              this.process = data.content.process;
              this.number = data.content.newest_step;
              this.executorData = data.content.executor;
              this.add_brife = data.content.add_brife;
              this.add_excutor = data.content.add_excutor;
              this.client_id = data.content.message.client_id;
              this.task_type = data.content.message.task_type;
              this.link_id = data.content.message.link_id;
              this.r_data.contract = data.content.message.contract;
              this.remarks = data.content.message.remarks;
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
      },
      /*// 人员列表接口
      personnel() {
        this.$get( 'manage/personnel')
          .then( (data) => {
            this.personAll = data.content;
          })
          .catch(() => {
          })
      },*/
      // 跳转项目或订单
      itemOrderDetail (){
        if(this.task_type === 1){
          this.$router.push({name: 'itemDetail', params: {id: this.link_id}})
        } else if (this.task_type === 2){
          this.$router.push({name: 'orderDetail', params: {id: this.link_id}})
        } else {
          this.$router.push({name: 'error-404'})
        }
      },
      // 任务明细列表接口
      taskList() {
        this.$get( 'task/breifDetail',{manage_id:this.$route.query.manage_id})
          .then( (data) => {
            this.breifDetail = data.content.detail;
            this.first_count = data.content.first_count;
            this.second_count = data.content.second_count;
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      // 跳转任务详情
      taskDetail (index, rows) {
        this.$router.push({name:'taskDetail', params : {id: rows[index].id}})
      },
      // 跳转客户详情
      clientDeatil () {
        this.$router.push({name: 'clientDetail', params: {id: this.client_id}})
      },
      // 新增执行人数据
      executorBasicData() {
        this.$get( 'manage/executorBasicData')
          .then( (data) => {
            this.BasicData = data.content.title;
            this.self_support = data.content.self_support;
            this.service_provider = data.content.service_provider;
            this.remark = data.content.remark;
            this.r_form.value = data.content.title.content[0].value;
          })
          .catch(() => {
            this.$message.error('服务器错误，请稍后重试');
          })
      },
      // 附件页
      fileListAll(){
        this.$get( 'manage/manageAttachment',{manage_id: this.$route.query.manage_id})
          .then( (data) => {
            if(data.code){
              this.annexData = data.content;
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
      },
      // 确定添加附件
      newFileAddT () {
        this.$post( 'manage/addAttachment',{manage_id: this.$route.query.manage_id, file: this.file, brife: this.brife})
          .then( (data) => {
            if(data.code){
              this.$message({
                message: data.errorMsg,
                type: 'success'
              });
              this.success = true;
              this.newFileAdd = false;
              this.brife = "";
              this.fileListAll();
              this.workOperateLog();
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
      },
      // 操作日志
      workOperateLog() {
        // console.log(this.$route.query.manage_id);
        this.$get( 'manage/manageOperateLog',{manage_id: this.$route.query.manage_id})
          .then( (data) => {
            // console.log(data);
            this.operateLog = data.content;
          })
          .catch((data) => {
            this.$message({
              message: data.errorMsg,
              type: 'warning'
            })
          })
      }
    },
    created () {
      // 派单需要的步骤数等三个传过来的参数
      this.r_data.manage_id = this.$route.query.manage_id;
      this.r_form.manage_id = this.$route.query.manage_id;
      this.cancelform.manage_id = this.$route.query.manage_id;
      this.r_data.step = this.$route.query.step;
      this.r_data.task_id = this.$route.query.task_id;
      this.$store.dispatch('dept',{});
      this.workData();
      this.workOperateLog();
      this.executorBasicData();
      this.taskList();
      this.fileListAll();
    }
  }
</script>

<style lang="less">
  .workInfo{
    .s_workinfo_step_0,.s_workinfo_step_1 ,.s_workinfo_step_2 ,.s_workinfo_step_3 ,.s_workinfo_step_4,.s_workinfo_step_5,.s_workinfo_step_6 {
      flex-basis: 180px !important;
    }
    .s_workinfo_step_0 .el-step__line-inner {
      border-image: linear-gradient(to right, #ffd801 , #ffb214) 30 30;
    }
    .s_workinfo_step_1 .el-step__line-inner {
      border-image: linear-gradient(to right,#ffb214,#ff9722) 30 30;
    }
    .s_workinfo_step_2 .el-step__line-inner {
      border-image: linear-gradient(to right,#ff9722,#ff6c38) 30 30;
    }
    .s_workinfo_step_3 .el-step__line-inner {
      border-image: linear-gradient(to right,#ff6c38,#ff464a) 30 30;
    }
    .s_workinfo_step_4 .el-step__line-inner {
      border-image: linear-gradient(to right,#ff464a,#ff2f55) 30 30;
    }
    .s_workinfo_step_5 .el-step__line-inner {
      border-image: linear-gradient(to right,#ff2f55,#f81841) 30 30;
    }
    .s_workinfo_step_6 .el-step__head {
      position: static;
    }
    .el-step__head {
      width: 180px;
    }
    .s_workinfo_step_0 .el-step__title.is-finish {
      color: #ffd801 !important;
    }
    .s_workinfo_step_0 .el-step__description.is-finish {
      color: #ffd801 !important;
    }
    .s_workinfo_step_1 .el-step__title.is-finish {
      color: #ffb214 !important;
    }
    .s_workinfo_step_1 .el-step__description.is-finish {
      color: #ffb214 !important;
    }
    .s_workinfo_step_2 .el-step__title.is-finish {
      color: #ff9722 !important;
    }
    .s_workinfo_step_2 .el-step__description.is-finish {
      color: #ff9722 !important;
    }
    .s_workinfo_step_3 .el-step__title.is-finish {
      color: #ff6c38 !important;
    }
    .s_workinfo_step_3 .el-step__description.is-finish {
      color: #ff6c38 !important;
    }
    .s_workinfo_step_4 .el-step__title.is-finish {
      color: #ff464a !important;
    }
    .s_workinfo_step_4 .el-step__description.is-finish {
      color: #ff464a !important;
    }
    .s_workinfo_step_5 .el-step__title.is-finish {
      color: #ff2f55 !important;
    }
    .s_workinfo_step_5 .el-step__description.is-finish {
      color: #ff2f55 !important;
    }
    .s_workIfo_img {
      width: 80px;
      height: 40px;;
    }
    .s_workinfo_step_0 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      left: 0;
      top: 3px;
      background: #ffd801;
      border-radius: 50%;
    }
    .s_workinfo_step_1 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      left: 0;
      top: 3px;
      background: #ffb214;
      border-radius: 50%;
    }
    .s_workinfo_step_2 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      left: 0;
      top: 3px;
      background: #ff9722;
      border-radius: 50%;
    }
    .s_workinfo_step_3 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      top: 3px;
      background: #ff6c38;
      border-radius: 50%;
    }
    .s_workinfo_step_4 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      top: 3px;
      background: #ff464a;
      border-radius: 50%;
    }
    .s_workinfo_step_5 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      top: 3px;
      background: #ff2f55;
      border-radius: 50%;
    }
    .s_workinfo_step_6 .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      border: 0;
      top: 3px;
      background: #ff2f55;
      border-radius: 50%;
    }
    .el-collapse-item__header {
      width: 100%;
      overflow: hidden;
      text-overflow:ellipsis;
      white-space: nowrap;
    }
    .s_workSummary {
      width: 100%;
      border-radius: 5px;
      margin-top: 10px;
      background: #fff;
    }
    .el-collapse {
      margin-left: 30px;
      border: 0;
    }
    .el-collapse, .el-collapse-item__header, .el-collapse-item__wrap {
      border-bottom: 0;
    }
    .workInfo .el-step__icon.is-text {
      width: 10px;
      height: 10px;
      margin-top: 6px;
      background: #ffd801;
      border: 0px;
    }
    .workInfo .el-step__icon-inner {
      display: none;
    }
    .workInfo_top{
      color: #FFF;
      border-radius: 8px;
      background: linear-gradient(to right,#6a85fd ,#76ccff) ;
      width: 100%;
    }
    .workNumber{
      width: 100%;
      font-size: 18px;
      font-weight: 800;
      height:45px;
    }
    .NumberIcon{
      margin-left: 25px;
      position: relative;
    }
    .number{
      position: relative;
      margin-left: 20px;
    }
    .workDetails{
      -webkit-box-shadow:0px -5px 6px rgba(51, 51, 51, .1);
      -moz-box-shadow:0px -5px 6px rgba(51, 51, 51, .1);
      box-shadow:0px -5px 6px rgba(51, 51, 51, .1);
      color: #000000;
      width:100%;
      height:250px;
      margin-top: 20px;
      background-color: #FFFFFF;
      border-top-left-radius: 45px;
      border-top-right-radius: 45px;
      border-bottom-left-radius: 8px;
      border-bottom-right-radius: 8px;
    }
    .s_workInfo_description {
      float: left;
      width: 700px;
      margin-left: 80px;
      margin-top: -20px;
    }
    .detailsMessageText{
      color: #999999;
    }
    .workDetailsContent{
      margin-left:2%;
      width: 96%;
      height:80%;
      border-bottom: 1px solid #f0f0f0;
    }
    .workDetailsLeft{
      position: relative;
      float: left;
      width:67%;
      height:80%;
    }
    .detailsMessageFirst{
      padding-top: 3%;
      margin-left: 30px;
      font-size: 16px;
    }
    .detailsMessage{
      margin-top: 1.2%;
      margin-left: 30px;
      font-size: 16px;
    }
    .workDetailsRight{
      position: relative;
      float: right;
      width:33%;
      height:80%;
    }
    .workDetailsButton{
      margin-left: 25px;
      width: 100px;
      height:44px;
      font-size: 16px;
      margin-top: 15%;
      border-radius: 10px;
      text-align: center;
      padding: 0px;
    }
    @media screen and (max-width: 1366px) {
      .workDetailsButton{
        margin-left: 20px;
        width: 80px;
        height:35.2px;
        font-size: 14px;
        margin-top: 15%;
        border-radius: 10px;
        text-align: center;
        padding: 0px;
      }
    }
    .workDetailsBottom{
      margin-left:2%;
      width: 96%;
      height:50px;
    }
    .workDetailsBottomMessage{
      width: 15%;
      position: relative;
      float: right;
      height: 100%;
      line-height: 50px;
      font-size: 16px;
    }
    .detailsMessageBottomText{
      color: #707970;
    }
    .workInfo_content{
      margin-top: 16px;
      border-radius: 10px;
    }
    .el-tabs__nav-wrap{
      background-color: #FFFFFF;
    }
    .el-tabs__active-bar {
      height:0px;
    }
    .el-tabs__item{
      font-size: 16px;
      font-weight: 600;
      color:#999999;
      text-align: center;
      margin-left: 20px;
      padding: 0px;
    }
    .el-tabs__item.is-active{
      color:#4380ff;
    }
    .el-tabs__header{
      margin: 0px;
    }
    .workInfo_content_title{
      margin-left: 10px;
      color: #333333;
      font-size: 16px;
      position: absolute;
      top:2px;
      left:22px;
    }
    .progress_bar{
      background-color: #FFFFFF;
      width: 100%;
      height:auto;
    }
    .first_style_title{
      padding-top:10px;
      font-weight: 600;
      width: 96%;
      margin-left: 2%;
      height:48px;
      border-bottom: 1px solid #f0f0f0;
      line-height: 50px;
      position: relative;
    }
    .first_style_content{
      width: 96%;
      margin-left: 2%;
      margin-top: 2px;
      padding-bottom: 20px;
    }
    .rocket{
      margin-top: 30px;
      padding: 0;
      width: 100%;
      height: 44px;
      position: relative;
    }
    .first_style_content .el-step__icon-inner {
      display: none;
    }
    .rocket_style0{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 0px;
    }
    .rocket_style1{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 165px;
    }
    .rocket_style2{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 340px;
    }
    .rocket_style3{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 520px;
    }
    .rocket_style4{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 700px;
    }
    .rocket_style5{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 880px;
    }
    .rocket_style6{
      width: 83px;
      height:39px;
      position: absolute;
      top: 5px;
      left: 1050px;
    }
    .implement{
      margin-top: 20px;
      border-radius: 10px;
      background-color: #FFFFFF;
      width: 100%;
      height:auto;
    }
    .tast{
      margin-top: 20px;
      border-radius: 10px;
      background-color: #FFFFFF;
      width: 100%;
      height:auto;
    }
    .el-collapse-item__header {
      color: #333;
      font-size: 16px;
    }
    .s_workInfoBtn {
      position: relative;
      top: -5px;
      left: 78px;
    }
  }
</style>
