 <!-- 

 			 
           


 


            

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


       -->