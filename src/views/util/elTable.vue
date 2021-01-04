<template>
    <div>
        <el-form size="small" inline label-width="110px" class="pt-2" >
            <el-form-item label="数据" prop="age">
                <el-input type="text" v-model.trim="ruleForms.age" autocomplete="off" style="width: 180px;"
                          placeholder="请输入"
                          clearable></el-input>{{ruleForms.numbers}}
            </el-form-item>
            <el-form-item>
                <el-button type="primary" size="mini" class="px-4" @click="searchbtn">查询</el-button>
            </el-form-item>
        </el-form>
        <el-row>
            <el-button type="primary"
                       @click="productAdd('add')"
                       plain
                       size="mini"
            >新增
            </el-button>
            <el-button type="primary"
                       @click="productAdd('require')"
                       plain
                       size="mini"
            >查询
            </el-button>
        </el-row>
        <el-table
                v-loading="isLoading"
                style="width: 100%;"
                height="580"
                :row-key="getRowKeys"
        :empty-text="emptyText"
        @selection-change="handleSelectionChange"
        :header-cell-style="{background:'#fafafa',color:'#606266'}"
        border
        :data="tableData">
        <el-table-column
                type="selection"
                :reserve-selection="true"
                width="50">
        </el-table-column>
        <el-table-column
                fixed
                type="index"
                align="center"
                label="序号"
                width="50">
            <template slot-scope="scope">
                <span>{{scope.$index+(currentPage - 1) * pagesize + 1}}</span>
            </template>
        </el-table-column>
        <el-table-column
                align="center"
                prop="order_code"
                label="采购时间"
                width="150">
        </el-table-column>
        <el-table-column
                align="center"
                prop="times"
                label="入库时间修改"
                width="150">
        </el-table-column>
        <el-table-column
                align="center"
                fixed="right"
                label="入库状态"
        >
            <template slot-scope="scope">
                <el-button @click="handleClick(scope.row)" type="text" size="small">商品详情</el-button>
            </template>
        </el-table-column>
        </el-table>
        <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[5,10, 20, 30, 40, 50, 100]"
                :page-size="pagesize"
                style="margin-bottom: 50px;"
                layout="total, sizes, prev, pager, next, jumper"
                :total="alltotal">
        </el-pagination>
    </div>
</template>
<script>
    export default {
        components: {},
        props: {},
        data() {
            return {
                ruleForms:{
                    age:'',
                },
                tableData:[],
                emptyText: '暂无数据',   //暂无数据时表格内文字
                isLoading: false,   //loading加载时蒙版
                alltotal: 0,   //请求过来所有的数据/总条数
                currentPage: 1, //当前页
                pagesize: 10,  //  每页的数据测试
            }
        },
        computed: {},
        watch: {},
        created() {
        },
        mounted() {
            // for(let i in 6){
            //     console.log(i)
            // }
        },
        destroyed() {
        },
        methods: {
            searchbtn(){
                this.ruleForms.numbers = 20
                console.log(this.ruleForms)
            },
            productAdd(type){
                switch (type) {
                    case 'require': {
                        let newList = [{order_code:'2020-12-29',times:[
                                    {istypes:'规格1',id:123},
                                    {istypes:'规格2',id:234},
                                    {istypes:'规格3',id:345}
                                ]},
                            {order_code:'2020-12-31',times: [
                                    {istypes:'服务',id:33},
                                    {istypes:'服务',id:34},
                                    {istypes:'服务',id:45},
                                    {istypes:'服务',id:56}
                                ]},
                            {order_code:'2020-12-31',times:[]}
                        ]
                        let lists = newList.map(i=>{
                            console.log(i)
                            let arr = []
                            i.times.forEach(item=>{
                                arr.push(item.istypes)
                                i.times = arr.toString()
                            })
                            return i
                        })
                        this.tableData = lists
                        console.log('点击查询事件触发')
                        break
                    }
                    case 'add': {
                        console.log('点击新增按钮')
                        break
                    }
                }
            },
            changePage(islength){  //islength  要删除的数据量
                let totalpage = Math.ceil((this.alltotal - islength) / this.pagesize)
                let pagenum = this.currentPage > totalpage ? this.currentPage : totalpage
                this.currentPage = pagenum < 1 ? 1 : pagenum
            },
            getRowKeys(row) {   //切换页面时不会清空当前选中状态
                return row.id
            },
            // 选中的复选框
            handleSelectionChange(val) {
                console.log(val)
            },
            //点击获取当前行的数据table编辑按钮
            handleClick(row) {
                console.log(row);
            },
            // 初始页currentPage、初始每页数据数pagesize和数据data
            handleSizeChange: function (size) {
                this.pagesize = size;
                // console.log(this.pagesize+"====每页几条") //每页下拉显示数据
            },
            handleCurrentChange: function (currentPage) {
                this.currentPage = currentPage;
                // console.log(this.currentPage+"=====前往第几页") //点击第几页
            },
        }
    };
</script>
<style scoped>
    .el-table{
        font-size: 12px;
    }
</style>
