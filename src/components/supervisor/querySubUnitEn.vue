<template>
    <div>
        <!-- 左侧查询 -->
        <el-row>
            <el-col :span="5">
                <el-input icon="search" v-model="searchContent" placeholder="请输入要查询的内容" class="search-input">
                </el-input>
            </el-col>
            <el-col :span="3">
                <el-button @click="handleNameSearch">查询</el-button>
            </el-col>
        </el-row>
        <el-row>
        <!-- 左侧表格 -->
            <el-col :span="10">
                <el-table
                :data="subUnitEnTable"
                stripe
                border
                highlight-current-row
                style="width:75%"
                @current-change="handleSelectionChange">
                    <el-table-column
                      label="序号"
                      type="index"
                      width="55">
                    </el-table-column>
                    <el-table-column label="企业名称" prop="name" ></el-table-column>
                </el-table>
                <!-- 分页 -->
                <el-row>
                <el-col :span="7">
                    <el-pagination
                      @size-change="handleSizeChange"
                      @current-change="handleCurrentChange"
                      :current-page="currentPage"
                      :page-sizes="[5,10,15,20,]"
                      :page-size="currentPageSize"
                      layout="total, sizes, prev, pager, next, jumper"
                      :total="totalNumber">
                    </el-pagination>
                </el-col>
            </el-row>
            </el-col>
            <!-- 右侧显示 -->
            <el-col :span="14">
                <!-- 年份季度查询行 -->
                <el-row>
                    <el-col :span="8">
                        <el-date-picker
                          v-model="searchYear"
                          align="right"
                          type="year"
                          placeholder="选择年">
                        </el-date-picker>年
                    </el-col>
                    <el-col :span="2">
                        <el-input type="number" max="4" min="1" v-model.number="searchQuarter"></el-input>
                    </el-col>
                    <el-col :span="4">
                        季度<el-button @click="handleYearSearch">查询</el-button>
                    </el-col>
                </el-row>
                <!-- 具体显示行 -->
                <el-row>
                    <el-col>
                        <el-row>
                            <el-col :span="6">生产的主要构件和部品系列</el-col>
                            <el-col :span="5"><span>生产线数量(条)</span></el-col>
                            <el-col :span="5"><span>生产能力</span></el-col>
                            <el-col :span="5"><span>应用规模</span></el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">整体墙板</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralWallNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralWallAbility}}万平方米</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralWallScale}}万平方米</el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">结构保温装饰一体化外墙</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integrativeExternalWallNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integrativeExternalWallAbility}}万平方米</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integrativeExternalWallScale}}万平方米</el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">预制楼梯</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.prebuiltStairsNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.prebuiltStairsAbility}}万平方米</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.prebuiltStairsScale}}万平方米</el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">整体厨房</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralKitchenNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralKitchenAbility}}台套/年</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralKitchenScale}}台套/年</el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">整体卫生间</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralToiletNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralToiletAbility}}台套/年</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralToiletScale}}台套/年</el-col>
                        </el-row>
                        <el-row >
                            <el-col :span="6">整体内装体系</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralInteriorDecorationNum}}</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralInteriorDecorationAbility}}万平方米</el-col>
                            <el-col :span="5">{{this.subUnitEnIndustrializationData.integralInteriorDecorationScale}}万平方米</el-col>
                        </el-row>
                    </el-col>
                </el-row>
            </el-col>
        </el-row>
        <msg-dialog ref="msgDialog"></msg-dialog>
    </div>
</template>
<script>
import msgDialog from '../common/msgDialog'
export default{
    data(){
        return {
            //表格数据
            subUnitEnTable:[],
            //当前页
            currentPage:1,
            //当前显示数据条数
            currentPageSize:5,
            //总条数
            totalNumber:0,
            //模糊查询企业名称
            searchContent:'',
            //选择年
            searchYear:'',
            //选择季节
            searchQuarter:'',
            //选择行
            selectedRows:null,
            //企业ID
            enId:'',
            //部品产业化的属性
            subUnitEnIndustrializationData:{integralWallNum:'',integrativeExternalWallNum:'',prebuiltStairsNum:'',integralKitchenNum:'',integralToiletNum:'',integralInteriorDecorationNum:'',integralWallAbility:'',integrativeExternalWallAbility:'',prebuiltStairsAbility:'',integralKitchenAbility:'',integralToiletAbility:'',integralInteriorDecorationAbility:'',integralWallScale:'',integrativeExternalWallScale:'',prebuiltStairsScale:'',integralKitchenScale:'',integralToiletScale:'',integralInteriorDecorationScale:''},
        }
    },
    methods:{
        //企业名称模糊查询按钮处理事件
        handleNameSearch(){
            if(this.searchContent==''){
                this.$refs.msgDialog.confirm("请输入要查询的内容")
            }else{
                var url = this.HOST+'/querySubUnitEnByName?nameQuery='+this.searchContent+'&page='+this.currentPage+'&rows='+this.currentPageSize
                this.$http.get(url).then(response=>{
                    this.subUnitEnTable = response.data.rows
                    this.totalNumber = response.data.total
                }).catch(error=>{
                    this.$refs.msgDialog.confirm("查询错误")
                })
            }
        },
        //年份季度查询按钮处理事件
        handleYearSearch(){
            if(this.searchYear==''|| this.searchQuarter ==''){
                this.$refs.msgDialog.confirm("请输入要查询的内容")
            }else{
                var year = this.moment(this.searchYear).format('YYYY')
                var url = this.HOST+'/queryQuarterSubUnitEnIn?enId='+this.enId+'&year='+year+'&quarter='+this.searchQuarter
                this.$http.get(url).then(response=>{
                    this.subUnitEnIndustrializationData = response.data
                }).catch(error=>{
                    this.$refs.msgDialog.confirm("查询错误")
                })
            }
        },
        //获取页面表格数据
        findAllSubUnitEns(){
            var url = this.HOST + '/displayAllSubUnitEns?page='+this.currentPage+"&rows="+this.currentPageSize
            this.$http.get(url).then(response=>{
                this.subUnitEnTable = response.data.rows
                this.totalNumber = response.data.total
            }).catch(error=>{
                this.$refs.msgDialog.confirm("查询错误")
            })
        },
        //显示条数发生改变时触发本事件
        handleSizeChange(newPageSize){
            this.currentPageSize = newPageSize
            this.findAllSubUnitEns()
        },
        //当前页数发生改变时触发本事件
        handleCurrentChange(newPage){
            this.currentPage = newPage
            this.findAllSubUnitEns()
        },
        //选择一行时触发的事件
        handleSelectionChange(selected){
            this.selectedRows = selected
            this.enId = this.selectedRows.id
        },

    },
    created(){
        this.findAllSubUnitEns()
    },
    components:{
        msgDialog
    }
}
</script>