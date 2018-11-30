// 客户列表组件
<template>
    <div class="dataList">
        <div class="btn">
            <div class="s_client_features_btn">
                <el-button type="primary" @click="toNewList">+新建</el-button>
                <el-button type="primary" @click="toggleSelection_all">转移</el-button>
            </div>
            <div clera="both"></div>
            <el-dialog title="" :visible.sync="dialogFormVisible" :modal="false">
                <el-form :model="form">
                    <el-form-item prop="client" label="人员"  class="s_client_item">
                        <el-select v-model="selectedOptions" @change="handleChange" clearable filterable placeholder="请选择" class="s_order_search">
                            <el-option
                                    v-for="item in person"
                                    :key="item.dd_id"
                                    :label="item.nickname"
                                    :value="item.dd_id">
                            </el-option>
                        </el-select>
                    </el-form-item>

                </el-form>
                <el-switch
                        v-model="date.project"
                        @change
                        active-text="是否转移全部项目">
                </el-switch>
                <el-switch
                        v-model="date.order"
                        @change
                        active-text="是否转移全部订单">
                </el-switch>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="transferTrue">确 定</el-button>
                </div>
            </el-dialog>
        </div>


        <div class="hitemlisttop">
            已选择<span>{{ number }}</span>项 <el-button type="text" @click="toggleSelection()">清空</el-button>
        </div>
        
        <el-card class="box-card">
            <div class="block">
                <span class="demonstration">
                    <el-table ref="multipleTable" :data="clientList" tooltip-effect="dark" style="width: 100%" @selection-change="handleSelectionChange" align="center">
                        <el-table-column type="selection" :selectable="checkboxT"></el-table-column>
                        <el-table-column fixed prop="code" label="客户编号"></el-table-column>
                        <el-table-column prop="company" label="客户名称"></el-table-column>
                        <el-table-column prop="location" label="所在地"></el-table-column>
                        <el-table-column prop="rank" label="客户级别"></el-table-column>
                        <el-table-column prop="trench" label="来源方式"></el-table-column>
                        <el-table-column prop="ask_method.name" label="咨询方式"></el-table-column>
                        <el-table-column prop="nickname" label="销售"></el-table-column>
                        <el-table-column prop="partner" label="共同负责人"></el-table-column>
                        <el-table-column prop="project_nums" label="意向项目数"></el-table-column>
                        <el-table-column prop="order_nums"  label="成单数"></el-table-column>
                         <el-table-column prop="ask_time" label="咨询时间"></el-table-column>
                        <el-table-column prop="create_time" width="180" sortable label="创建时间"></el-table-column>
                        <el-table-column fixed="right" label="操作">
                            <template slot-scope="scope">
                                <el-button type="text" size="small" @click="gotoInfo(scope.row)">查看</el-button>
                                <el-button v-if="scope.row.button_transfer" @click="transfer(scope.row)" type="text" size="small">转移</el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                </span>
                <el-pagination
                        background
                        @size-change="handleSizeChange"
                        @current-change="handleCurrentChange"
                        :current-page.sync="currentPage"
                        :page-sizes="[10, 20, 50, 100]"
                        :page-size="pageSize"
                        layout="total, sizes, prev, pager, next, jumper"
                        :total="count">
                </el-pagination>
            </div>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: 'clientListData',
        props: ['clientList','count','person','x_form','name'],
        data () {
            return {
                currentPage: 1,
                pageSize: 10,
                dialogFormVisible: false,
                client: [],
                number: 0,
                form: {
                    client: '',
                },
                date: {
                    project: false,
                    order: false,
                    id_all:[],
                    new_dd_id: '',
                },
                selectedOptions:[],
                ruleForm : {
                    client: '',
                },
                newNum: 0,
                selectionAll:[],
                isChack:false,
            }
        },
        methods: {
          checkboxT(row) {
            return row.button_transfer
          },
            toggleSelection(){
                this.$refs.multipleTable.clearSelection();
            },
            transfer(row) {
                this.dialogFormVisible = true;
                this.date.id_all = row.id;
            },
            gotoInfo(row){
                this.$router.push({name: 'clientDetail',params:{id:row.id}})
            },
            transferTrue(){
                this.$confirm('是否确认转移', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$post( 'client/transfer',this.date)
                        .then( (data) => {
                            if(data.code){
                                this.$message({
                                    type: 'success',
                                    message: '转移成功!'
                                });
                                this.dialogFormVisible = false;
                                this.form.client = '';
                                this.client = [];
                                this.date.project = false;
                                this.date.order = false;
                                this.client_list();
                            } else {
                                this.$message({
                                    message: data.errorMsg,
                                    type: 'warning'
                                });
                            }
                        })
                        .catch ( (error) => {
                        })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消转移'
                    });
                });
            },
            handleSizeChange(val) {
                this.pageSize = val;
                this.client_list();
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.client_list();
            },
            // 客户列表接口连接
            client_list () {
                //x_form 是为了查询过后的 再点击分页 能让分页
                let x_form = this.x_form;
                x_form.pageSize = this.pageSize;
                x_form.currentPage = this.currentPage;
                x_form.range = this.name;
                this.$get( 'client/list',x_form)
                    .then( (data) => {
                        if(data.code){
                            this.$emit("client",[data.content.data,data.content.count]);
                        }
                    })
                    .catch ( (data) => {
                        this.$message.error('服务器错误，请稍后重试');
                    })
            },
            // 列表的选择状态
            handleSelectionChange(val) {
                this.date.id_all = [];
                if(val.length > 0){
                    for(let i = 0; i < val.length; i++){
                        this.date.id_all.push(val[i].id);
                        this.date.id_all = Array.from(new Set(this.date.id_all))
                    }
                }else{
                    this.selectionAll = [];
                }
                this.number = val.length;
            },
            // 批量转移客户
            toggleSelection_all(){
                if(this.date.id_all.length > 0){
                    this.dialogFormVisible = true;
                } else {
                    this.$message.error('这是转移多个客户，请选择转移客户');
                }
            },
            // 负责人的级联选择
            handleChange(value) {
                this.date.new_dd_id = value;
            },
            toNewList() {
                this.$router.push({name: 'newClient'});
            },
        },
        created(){
          this.client_list();
        }
    }
</script>
<style scoped>
    .btn{
        margin-top: 15px;
        margin-bottom: 15px;
    }
    .s_client_loading{
        width: 100%;
        height: 50px;
        text-align: left;
        line-height: 50px;
        text-indent:2em;
        background: #e1e1e1;
    }
    .s_client_loading span{
        color: #fff;
        margin-left: 10px;
    }
    .el-pagination{
        padding: 20px;
        text-align: center
    }
    .box-card {
        margin: 0;
    }
</style>
