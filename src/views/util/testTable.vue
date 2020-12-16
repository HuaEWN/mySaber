
<template>
    <basic-container>
        <template>
            <el-form :model="ruleForm"
                     inline
                     label-width="110px"
                     size="small"
                     ref="addForm"
            >
                <el-form-item label="商户名称" prop="business_id">
                    <el-select v-model.trim="ruleForm.business_id"
                               placeholder="请选择"
                               style="width: 180px"
                               clearable
                    >
                        <el-option v-for="item of merchantNameList" :label="item.key" :key="item.value"
                                   :value="item.key"/>
                    </el-select>
                </el-form-item>
                <el-form-item label="余额预警" prop="balance_warning">
                    <el-select v-model.trim="ruleForm.balance_warning"
                               filterable
                               placeholder="请选择"
                               style="width: 180px"
                    >
                        <el-option label="请选择" value=""></el-option>
                        <el-option label="有" value="1"></el-option>
                        <el-option label="无" value="0"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="queryBtn" size="mini" style="width: 80px; height: 30px;">查询
                    </el-button>
                    <el-button size="mini" @click="resetBtn" style="width: 80px; height: 30px;">重置</el-button>
                </el-form-item>
            </el-form>
        </template>
        <!--table-loading 表格加载过程中的loading
         :page 分页的变量
         @search-change  点击搜索后触发该事件
         @search-reset  清空搜索回调方法
         @size-change  每页条数改变时触发
         @current-change  切换页面时触发
         @refresh-change  刷新页面时触发
         @selection-change  复选框选中触发
         -->
        <avue-crud :data="tableData"
                   :option="option"
                   :table-loading="pageLoading"
                   ref="crud"
                   :page="page"
                   @size-change="sizeChange"
                   @current-change="currentChange"
                   @refresh-change="refreshChange"
                   @selection-change="selection"
        >
            <!--  slot设置为menuLeft时为中间左侧搜索按钮   menuRight为中间右侧，不常用 -->
            <template slot="menuLeft">
                <el-button type="primary"
                           size="mini"
                           plain
                           @click="operationBtn('add')">新增
                </el-button>
            </template>

            <!--设置table中内容自定义-->
            <template slot="warnAmount" slot-scope="scope">
                <span v-if="scope.row.warnAmount!==''">{{scope.row.warnAmount}}</span>
                <span v-else>无</span>
            </template>
            <template slot="inform" slot-scope="scope">
                <span v-if="scope.row.warnAmount!==''">钉钉群</span>
                <span v-else>无</span>
            </template>

            <!--设置自定义表格里面操作（表格最右边操作栏）-->
            <template slot-scope="scope" slot="menu">
                <el-dropdown @command="(value)=>handleClick(value,scope.row)">
                      <span class="el-dropdown-link">
                        操作<i class="el-icon-arrow-down el-icon--right"></i>
                      </span>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item command="set">设置余额预警</el-dropdown-item>
                        <el-dropdown-item command="edit">编辑余额预警</el-dropdown-item>
                        <el-dropdown-item command="delete">删除余额预警</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
            </template>
        </avue-crud>

        <el-dialog title="过期提醒" :visible.sync="visible"
                   v-if="visible"
                   width="30%"
                   :destroy-on-close="true"
                   :modal-append-to-body="false"
                   :close-on-click-modal="false"
                   append-to-body>

        </el-dialog>

    </basic-container>
</template>
<script>

    export default {
        components: {},
        props: {},
        data() {
            return {
                merchantNameList: [],  //商户名称下拉框内容
                ruleForm: {
                    business_id: '',
                    balance_warning: '',
                },
                tableData: [  //table数据
                ],
                page: {  //分页信息
                    pageSize: 10,
                    currentPage: 1,
                    total: 0
                },
                pageLoading: false,  //表格加载过程中的loading
                option: {   // 表格配置
                    height: 'auto',  //表格高度属性
                    calcHeight: 'auto',   //表格高度差
                    searchLabelWidth: 100,  //搜索框文字宽度
                    tip: false,    //table上面显示已选择信息（不加）
                    searchShow: true,  //首次加载页面是否显示顶部搜索选框及按钮
                    searchMenuSpan: 6,  //顶部搜索按钮距左边的间距
                    border: true,  //表格边框是否显示
                    index: true,  //是否显示表格序号
                    selection: false,  //设置行是否可勾选
                    editBtn: false,   //编辑按钮   按钮这块自己写不用官方文档所以都是false
                    addBtn: false,   //新增按钮
                    // viewBtn: true,
                    delBtn: false,  //删除按钮
                    dialogWidth: 900,  //设置弹出dialog宽度（这里用自己写的不考虑这个信息）
                    menuWidth: 200,   //操作菜单栏的宽度table操作栏宽度
                    dialogClickModal: false,  //是否可以通过点击modal关闭Dialog（dialog写自己的不用考虑）
                    menuAlign:'center',  //table操作栏文字居中对齐
                    align:'center',  //table表头文字居中对齐
                    column: [
                        {    //下拉框数据是从接口获取时设置dicUrl属性
                            label: '商户名称',
                            prop: 'name',
                            width: '240',
                            // slot: true, // 自定义列
                        },
                        {
                            label: '当前账户余额',
                            width: '240',
                            prop: 'balance',
                        }, {
                            label: '授信余额',
                            width: '240',
                            prop: 'creditAmount',
                        }, {
                            label: '余额预警值',
                            width: '240',
                            prop: 'warnAmount',
                            slot: true, // 自定义列
                            // formslot:true // 自定义表单
                        }, {
                            label: '通知方式',
                            width: '240',
                            prop: 'inform',
                            slot: true, // 自定义列
                            // formslot:true // 自定义表单
                        }
                    ]
                }
            }
        },
        computed: {},
        watch: {},
        created() {
        },
        mounted() {
            this.queryBtn()
        },
        destroyed() {
        },
        methods: {
            //头部表单查询
            queryBtn(){
                this.tableData = [
                    {name: 123, balance: 10.00,warnAmount: ''},
                    {name: 12345, balance: 14.00,warnAmount: '12'},
                    {name: 5, balance: 24.00,warnAmount: '22'}
                ];
                this.page.pageSize = 10;
                this.page.currentPage = 1;
                this.page.total = 3;
            },
            //每页条数改变时触发
            sizeChange(pageSize){
                console.log(pageSize)
            },
            //切换分页时触发
            currentChange(page){
                console.log(page)
            },
            //刷新页面时触发
            refreshChange(page){
                console.log(12345)
            },
            // 复选框勾选时触发
            selection(selection){
                console.log(selection)
            }
        }
    };
</script>
<style scoped>
</style>
