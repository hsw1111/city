<template>
  <div class="queryLists">
   <div v-title data-title="订单数据"></div> 
   <!-- <div v-show="notice" class="el-notification" style="top: 16px; z-index: 2000;"><i class="el-notification__icon el-icon-warning"></i><div class="el-notification__group is-with-icon"><h2 class="el-notification__title">温馨提示</h2><div class="el-notification__content">实际收益=用户实际支付金额，为本订单扣除了优惠券、赠送余额支付的金额。</div></div></div> -->
    <h3>
      <span class="queryLists_info">订单数据每10分钟自动统计</span>
      <button class="btn_list" @click="handeClick">查看统计图</button>
      <h1>{{$store.state.moment}}</h1>
    </h3>
    <div>
       <el-popover
          slot="reference"
          ref="popover1"
          placement="top-end"
          width="252"
          title="数据项说明"
          trigger="hover">
          <p>实际收益=用户实际支付金额,</p>
          <p>为本订单扣除了优惠券、赠送余额支付的金额。</p>
        </el-popover>
        <i class="icon iconfont icon-wenhao" v-popover:popover1 style='cursor:pointer;margin-left:0px;color:orange;font-size:18px;vertical-align:middle;float:right'></i>
      <el-table
        :data="lists"
        v-loading="loading2"
        element-loading-text="拼命加载中"
        :empty-text="emptyText"
        style="width: 100%"
        >
        <el-table-column
          prop="statisticId"
          label="订单日期"
          min-width="120">
        </el-table-column>
        <el-table-column
          prop="totalBill"
          label="订单数"
          min-width="120">
              <template slot-scope="scope">
                {{new Number(scope.row.totalBill).thousand()}}
              </template>
        </el-table-column>
        <el-table-column
          prop="totalMoney"
          label="订单总额(元)">
             <template slot-scope="scope">
                {{new Number(scope.row.totalMoney).thousandFormat()}}
              </template>
        </el-table-column>
        <el-table-column
          prop="totalDiscount"
          label="优惠券支付总额(元)">
             <template slot-scope="scope">
                {{new Number(scope.row.totalDiscount).thousandFormat()}}
              </template>
        </el-table-column>
         <el-table-column
          label="赠送金额支付(元)">
             <template slot-scope="scope">
                {{new Number(scope.row.grantAmount).thousandFormat()}}
              </template>
        </el-table-column>
        <el-table-column
          min-width="80"
          label="实际收益(元)"
          prop='actualMoney'
          >
           <!-- :render-header="rendHeader" -->
             <template slot-scope="scope">
                {{new Number(scope.row.actualMoney).thousandFormat()}}
              </template>
        </el-table-column>
      </el-table>
    </div>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page.sync="currentPage3"
      :page-size="10"
      layout="prev, pager, next, jumper"
      :total="totalItems"
      v-show="pageShow"
      >
    </el-pagination>
		<div id="earD_page">
		
		</div>
  </div>
</template>
<style scoped>
.queryLists_info {
    float: left;
    color: #ddd;
    font-weight: 400;
    font-size: 12px;
    line-height: 50px;
 }
div.el-notification{right:-330px;}
.datashow {
  /* width: 100%; */
  height: 60px;
  line-height: 60px;
  border: 1px solid #dfe6ec;
  border-top: none;
}

.datashow p {
  text-align: center;
  color: #5e7382;
}

div.queryLists h3 {
  text-align: right;
  margin-bottom: 2px;
}

div.queryLists {
  padding: 17px 20px 20px 20px;
  background: #fff;
  border: 1px solid #e7ecf1;
  border-bottom: none;
  
}

div.queryLists h3 button {
  margin-right: 0px;
  width: 137px;
}



#earD_page {
  padding: 14px 0px 0px 0px;
  background: #fff;
  /*border: 1px solid #e7ecf1;*/
  border-top: none;
  
  margin-left: -9px;
}

.btn_list {
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  border: 1px solid #c4c4c4;
  color: #1f2d3d;
  font-size: 14px;
  margin: 0;
  padding: 10px 15px;
  outline: none;
  border-radius: 4px;
}

.btn_list:hover {
  color: rgba(255,140,0, 0.8);
  border: 1px solid rgba(	255,140,0, 0.8);
}
.el-pagination{    margin-left: 0px;padding-left: 0;margin-top: 14px;}
</style>
<script>
// import $ from ''
import request from 'superagent'
// import moment from 'moment'
import { host } from '../../../config/index.js'
import {mapGetters,mapActions} from 'vuex'
export default {
  data () {
    return {
      lists: [],
      notice:false,
      emptyText: ' ',
      pageTotal: '',
      totalItems:1,
      noDate: false,
      loading2: false,
      currentPage3: 1,
      pageShow: false
    }
  },
  computed:{
    ...mapGetters(['cityId'])
  },
  methods: {
    loadData(){
      var type = this.$store.state.users.consumeDataType
    this.currentPage3 = 1
    var that = this
    console.log(this.cityId)
    request
      .post(host + 'beepartner/franchisee/statistics/franchiseeStatistics')
      .withCredentials()
      .set({
        'content-type': 'application/x-www-form-urlencoded'
      })
      .send({
        cityId:this.cityId,
        'startTimeStr': this.$store.state.users.timeline.startTime,
        'endTimeStr':this.$store.state.users.timeline.endTime,
        'type': type,
          showType:'table',
          currentPage:1
      })
      .end((error, res) => {
        if (error) {
          console.log('error:', error)
          this.loading2 = false
        } else {
          this.loading2  = false
            var totalPage = JSON.parse(res.text).totalPage
            this.totalItems = Number(JSON.parse(res.text).totalItems)
            if (totalPage>1) {
              that.pageShow = true
              this.emptyText = ' '
            } else {
              that.pageShow = false
              this.emptyText = '暂无数据'
            }
            that.lists = []
            var arr = JSON.parse(res.text).data||[]
            that.$store.dispatch('consumeData_action', arr)
            that.lists = that.$store.state.users.consumeData
        }
      })
    },
    //  mouseLeaveHandler(){
    //   $('div.el-notification').stop().animate({right:'-330px'},500,function(){
    //      this.notice = false
    //   })
    // },
    // mouseEnterHandler(){
    //   console.log(1)
    //   this.notice = true
    //   $('div.el-notification').stop().animate({right:'1px'},500)
    // },
    // rendHeader(h,{column,$index}){
    //    return  h('div',{
    //      class:{
    //        tips:true,
    //        cell:true
    //      },
    //      attrs:{
    //        style:'background:#eee;margin-left:-20px;width:240px;'
    //      }
    //    },[
    //      h('span','实际收益（元）'),
    //      h('i',{
    //        class:{
    //          'icon iconfont icon-wenhao':true
    //        },
    //        attrs:{
    //          style:'cursor:pointer;margin-left:0px;color:orange;font-size:18px;vertical-align:middle'
    //        },
    //        on: {
    //         mouseenter: this.mouseEnterHandler,
    //         mouseleave: this.mouseLeaveHandler
    //       }
    //      })
    //    ])
    // },
    handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`);
      },
    handeClick () {
      var type = this.$route.query.type
      this.$router.push('/index/consumeData/queryCharts?type=' + type)
    },
    dataUpdate () {
      var flag = true
      this.loading2 = true
      if (this.$route.query.type === undefined) {
        return
      } else if (flag === true) {
        var type = this.$store.state.users.consumeDataType
        request
        .post(host +'beepartner/franchisee/statistics/franchiseeStatistics')
         .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
        .send({
         'startTimeStr': this.$store.state.users.timeline.startTime,
          'endTimeStr': this.$store.state.users.timeline.endTime,
          'type': type,
          showType:'table',
          currentPage:1,
          cityId:this.cityId
        })
        .end((error, res) => {
          // console.log('this is entry')
          if (error) {
            console.log('error:', error)
            this.loading2 = false
            this.emptyText = ' 暂无数据'
          } else {
            var arr = JSON.parse(res.text).data
            this.loading2 = false  
            var pageNumber = JSON.parse(res.text).totalPage
            this.totalItems = Number(JSON.parse(res.text).totalItems)
            if(pageNumber>1){
              this.pageShow = true
            }else {
              this.pageShow = false
              this.emptyText = ' 暂无数据'
            }
            this.$store.dispatch('consumeData_action', arr)
            this.lists = this.$store.state.users.consumeData
            flag = true
          }
        })
      } else {
        return
      }
    },
    // getDateMount () {
    //   this.loading2 = true
    //    //alert(this.$store.state.consumeDataType)
    //   var timeType = this.$store.state.users.consumeDataType
    //   request
    //     .post(host +'beepartner/franchisee/statistics/franchiseeStatistics')
    //      .withCredentials()
    //       .set({
    //         'content-type': 'application/x-www-form-urlencoded'
    //       })
    //     .send({
    //      'startTimeStr': this.$store.state.users.timeline.startTime,
    //       'endTimeStr': this.$store.state.users.timeline.endTime,
    //       'type': type,
    //       showType:'table',
    //       currentPage:1
    //     })
    //     .end((error, res) => {
    //       // console.log('this is entry')
    //       if (error) {
    //         console.log('error:', error)
    //         this.loading2 = false
    //         this.emptyText = ' 暂无数据'
    //       } else {
    //         var arr = JSON.parse(res.text).data
    //         this.loading2 = false
    //         // loading关闭
    //         this.loading2 = false
    //         return
    //         var pageNumber = JSON.parse(res.text).totalPage
    //         this.totalItems = Number(JSON.parse(res.text).totalItems)
    //         if(pageNumber>1){
    //           this.pageShow = true
    //         }else {
    //           this.pageShow = false
    //           this.emptyText = ' 暂无数据'
    //         }
    //         this.$store.dispatch('consumeData_action', arr)
    //         this.lists = this.$store.state.users.consumeData
    //       }
    //     })
    // },
    time () {
      var type = this.$route.query.type
      this.loading2 = true
      var that = this
      this.currentPage3 = 1
      request
        .post(host + 'beepartner/franchisee/statistics/franchiseeStatistics')
        .withCredentials()
        .set({
          'content-type': 'application/x-www-form-urlencoded'
        })
        .send({
          'startTimeStr': this.$store.state.users.timeline.startTime.length>0?this.$store.state.users.timeline.startTime:'',
          'endTimeStr': this.$store.state.users.timeline.endTime.length>0?this.$store.state.users.timeline.endTime:'',
          'type': type,
          'showType':'table',
          'currentPage':1,
          'cityId':this.cityId
        })
        .end((error, res) => {
          if (error) {
            console.log('error:', error)
            this.loading2 = false
          } else {
            this.loading2  = false
              var totalPage = JSON.parse(res.text).totalPage
               this.totalItems = Number(JSON.parse(res.text).totalItems)
              if (totalPage>1) {
                that.pageShow = true
                this.emptyText = ' '
              } else {
                that.pageShow = false
                this.emptyText = '暂无数据'
              }
              that.lists = []
              var arr = JSON.parse(res.text).data||[]
              that.$store.dispatch('consumeData_action', arr)
              that.lists = that.$store.state.users.consumeData
          }
        })
    }
  },
  mounted () {
   setTimeout(()=>{
      this.loadData()
   },200)
  
  },
  watch: {
    'cityId':{
      handler:function(n,o){
       this.loadData()
      },
      deep:true,
    },
    '$route': 'dataUpdate',
    '$store.state.users.consumeDataType':'time',
    '$store.state.users.timeline':'time',
    currentPage3: {
      handler: function(val,oldVal){
      var type = this.$route.query.type
      this.loading2 = true
      var that = this
      request
        .post(host + 'beepartner/franchisee/statistics/franchiseeStatistics')
        .withCredentials()
        .set({
          'content-type': 'application/x-www-form-urlencoded'
        })
        .send({
          cityId:this.cityId,
          'startTimeStr': this.$store.state.users.timeline.startTime.length>0?this.$store.state.users.timeline.startTime:'',
          'endTimeStr': this.$store.state.users.timeline.endTime.length>0?this.$store.state.users.timeline.endTime:'',
          'type': type,
            showType:'table',
            currentPage:val
        })
        .end((error, res) => {
          if (error) {
            console.log('error:', error)
            this.loading2 = false
          } else {
            this.loading2  = false
              var totalPage = JSON.parse(res.text).totalPage
               this.totalItems = Number(JSON.parse(res.text).totalItems)
              if (totalPage>1) {
                that.pageShow = true
                this.emptyText = ' '
              } else {
                that.pageShow = false
                this.emptyText = '暂无数据'
              }
              that.lists = []
              var arr = JSON.parse(res.text).data||[]
              that.$store.dispatch('consumeData_action', arr)
              that.lists = that.$store.state.users.consumeData
          }
        })
      },
      deep: true
    }
  }
}
</script>
