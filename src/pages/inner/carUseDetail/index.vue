<template>
  <div>
    <div v-title data-title="车辆详情"></div>
    <!-- <div v-show="notice" class="el-notification" style="top: 16px; z-index: 2000;"><i class="el-notification__icon el-icon-warning"></i><div class="el-notification__group is-with-icon"><h2 class="el-notification__title">温馨提示</h2><div class="el-notification__content">实际收益=用户实际支付金额，为本订单扣除了优惠券、赠送余额支付的金额。</div></div></div> -->
      <div class="carUseDetail">
        <div class="detailTitle">
          <h3>车辆详情</h3>
        </div>
        <el-row>
          <el-col :span="24">
            <table>
              <tbody>
                <tr>
                  <td class="lang">
                    <span class="prex">车辆号:</span>{{bikeInfo.code}}</td>
                  <td>
                    <span class="prex">终端编号:</span>{{bikeInfo.boxCode}}</td>
                </tr>
                <tr>
                  <td class="lang">
                    <span class="prex">车辆型号:</span>{{bikeInfo.generationsName}}</td>
                  <td>
                    <span class="prex">车型:</span>{{bikeInfo.model}}</td>
                </tr>
                <tr>
                  <td class="lang">
                    <span class="prex">上线日期:</span>{{bikeInfo.onlineTime}}</td>
                  <td>
                    <span class="prex">报废日期:</span>2020-01-01</td>
                </tr>
                <tr>
                  <td class="lang">
                    <span class="prex">所属区域:</span>{{bikeInfo.cityName}}</td>
                  <td>
                    <span class="prex">车辆位置:</span>{{bikeInfo.location}}</td>
                </tr>
              </tbody>
            </table>
          </el-col>
          <el-col>
            
          </el-col>
          <!-- <el-col :span="6" class="battery">
            <ul>
              <li>
                <span class="online">在线</span>
              </li>
              <li>
                <span class="lend">带出租</span>
              </li>
              <li>
                <span class="capacity">电池电量: 50V</span>
              </li>
            </ul>
          </el-col> -->
        </el-row>
        <el-popover
          slot="reference"
          placement="top-end"
          width="252"
          title="数据项说明">
          <p>实际收益=用户实际支付金额,</p>
          <p>为本订单扣除了优惠券、赠送余额支付的金额。</p>
        </el-popover>
        <el-row class="record">
          
          <el-tabs v-model="activeName">
            <el-tab-pane class="incomeRecord recodeTable" label="收益记录" name="first">
              <!-- <i class="icon iconfont icon-wenhao" v-popover:popover1 style='cursor:pointer;margin-left:0px;color:orange;font-size:18px;vertical-align:middle;float:right'></i> -->
              <el-table
              :data="tableData"
              style="width:100%"
              v-loading="loading2"
              :empty-text='emptyText'
              element-loading-text="拼命加载中"
            >
              <el-table-column prop="placeOrderTimeStr" label="订单结束时间" min-width="15%">
              </el-table-column>
              <el-table-column label="骑行时间(分钟)" prop="rideTime"
              min-width="15%"
              >
                <template slot-scope="scope">
                    {{(scope.row.rideTime).thousand()}}
                </template>
              </el-table-column>
              <el-table-column label="骑行里程(米)" min-width="15%" prop="rideMileage">
                <template slot-scope="scope">
                    {{(scope.row.rideMileage).thousand()}}
                </template>
              </el-table-column>
              <el-table-column label="订单费用(元)" prop="actualAmount" min-width="10%">
                <template slot-scope="scope">
                    {{(scope.row.actualAmount).thousandFormat()}}
                </template>
              </el-table-column>
              <el-table-column label="优惠券支付(元)" prop="couponAmount" min-width="12%">
                <template slot-scope="scope">
                    {{(scope.row.couponAmount).thousandFormat()}}
                </template>
              </el-table-column>
              <el-table-column label="赠送金额支付(元)"  min-width="12%">
                  <template slot-scope="scope">
                    {{(scope.row.grantAmount).thousandFormat()}}
                  </template>
              </el-table-column>
              <el-table-column label="实际收益(元)" prop="balanceAmount"
                 min-width="15%"
                 :render-header="rendHeader"
              >
                <template slot-scope="scope">
                   {{(scope.row.balanceAmount).thousandFormat()}}
                </template>

              </el-table-column>
            </el-table>
              <!--<table>
                <thead>
                  <tr>
                    <th>下单时间</th>
                    <th>骑行时间（分钟）</th>
                    <th>里程（公里）</th>
                    <th>订单费用</th>
                    <th>优惠卷支付</th>
                    <th>实际收益
                      <el-tooltip placement="top">
                        <div slot="content">实际收益就是用户实际支付金额，但不等于订单费用减去优惠券支付金额；<br/>优惠券支付的金额可能大于订单费用，例如某笔订单骑行费用是3元，<br/>然后用户可能是用5元的优惠券抵扣的。</div>
                        <span class="help">?</span>
                      </el-tooltip>
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-bind:key="index" v-for="(item,index) of incomeTableData">
                    <td>{{item.date}}</td>
                    <td>{{item.time}}</td>
                    <td>{{item.mileage}}</td>
                    <td>{{item.money}}</td>
                    <td>{{item.actualAmount}}</td>
                    <td>{{item.couponAmount}}</td>
                  </tr>
                </tbody>
              </table>-->
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
            </el-tab-pane>
            <!-- <el-tab-pane label="换电记录" name="second" class="recodeTable">
              <table>
                <thead>
                  <tr>
                    <th>换电日期</th>
                    <th>换电人员</th>
                    <th>换电成功</th>
                    <th>电池容量</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-bind:key="index" v-for="(item,index) of batteryTableData">
                    <td>{{item.date}}</td>
                    <td>{{item.person}}</td>
                    <td>{{item.status}}</td>
                    <td>{{item.capacity}}</td>
                  </tr>
                </tbody>
              </table>
            </el-tab-pane>
            <el-tab-pane label="维修记录" name="third" class="recodeTable">
              <table>
                <thead>
                  <tr>
                    <th>维修日期</th>
                    <th>维修时间</th>
                    <th>维修地点</th>
                    <th>维修人员</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-bind:key="index" v-for="(item,index) of repairTableData">
                    <td>{{item.date}}</td>
                    <td>{{item.time}}</td>
                    <td>{{item.place}}</td>
                    <td>{{item.person}}</td>
                  </tr>
                </tbody>
              </table>
            </el-tab-pane> -->
          </el-tabs>
        </el-row>
        <div id="carUseDetail_page">
          <div class="M-box"></div>
        </div>
      </div>
  </div>
</template>
<script>
// import $ from 'jquery'
// import moment from 'moment'
import request from 'superagent'
import {host} from '../../../config/index'
import {thousand,thousandFormat} from '../../../util/util.js'
export default {
  data: function () {
    return {
      notice:false,
      loading2: false,
      currentPage3:1,
      pageShow:false,
      totalItems:1,
      emptyText:' ',
      tableData:[],
      router: this.$route.query.carNum,
      activeName: 'first',
      incomeTableData: [],
      repairTableData: [],
      batteryTableData: [],
      pageTotal: '',
      bikeInfo:{
         code: '',
         boxCode:'',
         onlineTime:'',
         endTime:' 2020-2-2',
         cityName: '',
         location:'',
         generationsName:'',
         model: '',
         location:''
      }
    }
  },
  mounted: function () {
    console.log(this.$store)
    //this.bikeInfo.code = '000000009' 
    this.loading2 = true
    this.bikeInfo.code = this.$route.query.code
    request.post(host + 'beepartner/franchisee/bike/getBikeDetail')
     .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
      .send({
        code:this.bikeInfo.code,
      })
      .end((error,res)=>{
        if(error){
          console.log(error)
           this.loading2 = false
           this.emptyText = '暂无数据'
        }else {
           this.loading2 = false
          this.bikeInfo = Object.assign({},JSON.parse(res.text).bike,{onlineTime:moment(JSON.parse(res.text).bike.onlineTime).format('YYYY-MM-DD')})
          this.tableData = JSON.parse(res.text).data
          this.totalPage = JSON.parse(res.text).totalPage
          this.totalItems = Number(JSON.parse(res.text).totalItems)
            if(this.totalPage>1){
              this.pageShow  = true
               this.emptyText = ' '
            }else {
              this.pageShow = false
               this.emptyText = '暂无数据'
            }
          }
      })
  },

  methods: {
   mouseLeaveHandler() {
       $('div.el-popover').css({
         "display":"none",
       })
    },
    mouseEnterHandler() {
      $('div.el-popover').css({
         right:101,
         top:200,
         "display":"block",
       })
    },
    rendHeader(h, { column, $index }) {
      return h('div', {
        class: {
          tips: true,
          cell: true
        },
        attrs: {
          style: 'background:#eee;margin-left:-20px;width:240px;',
          
        }
      }, [
          h('span', '实际收益(元)'),
          h('i', {
            class: {
              'icon iconfont icon-wenhao': true
            },
            attrs: {
              style: 'cursor:pointer;margin-left:0px;color:orange;font-size:18px;vertical-align:middle',
            },
            on: {
              mouseenter: this.mouseEnterHandler,
              mouseleave: this.mouseLeaveHandler
            }
          })
        ])
  },
    handleSizeChange(val) {
        // console.log(`每页 ${val} 条`);
      },
      handleCurrentChange(val) {
        // console.log(`当前页: ${val}`);
      },
    getBikeEarnings (page) {
      request
        .post(host + 'franchisee/bikeManager/bikeRevenueRecord?page=' + page)
        .send({
          'franchiseeId': '123456',
          'userId': 'admin',
          'code': this.$route.query.code
        })
        .end((err, res) => {
          if (err) {
            console.log('err:' + err)
          } else {
            // console.log(res.text)
            // console.log(JSON.parse(res.text).list)
            var newArr = JSON.parse(res.text).list
            var arrDeled = []
            for (var i = 0; i < newArr.length; i++) {
              var obj = {}
              obj.money = newArr[i].money
              obj.mileage = newArr[i].mileage + '里'
              obj.date = moment(newArr[i].chargeTime).format('YYYY-MM-DD HH:MM:SS')
              obj.time =  Math.floor((newArr[i].time) / 60000) + ' 分钟'
              /*
                以下为新增优惠卷字段，需跟后台确认后渲染数据
              */
              obj.actualAmount = newArr[i].actualAmount
              obj.couponAmount = newArr[i].couponAmount
              arrDeled.push(obj)
            }
            this.incomeTableData = arrDeled
            var pageNumber = JSON.parse(res.text).totalPage
            this.pageTotal = pageNumber
            if (pageNumber < 10) {
              return
            } else {
              $('.M-box').pagination({
                pageCount: pageNumber,
                jump: true,
                coping: true,
                homePage: '首页',
                endPage: '尾页',
                prevContent: '«',
                nextContent: '»'
              })
            }
          }
        })
    },
    // getReplaceBatteryRecord (page) {
    //   request
    //     .post('http://192.168.3.52:7099/franchisee/bikeManager/replaceBatteryRecord?page=' + page)
    //     .send({
    //       'franchiseeId': '123456',
    //       'userId': 'admin',
    //       'code': this.$route.query.code
    //     })
    //     .end((err, res) => {
    //       if (err) {
    //         console.log('err:' + err)
    //       } else {
    //         // console.log(res.text)
    //         // console.log(JSON.parse(res.text).list)
    //         var newArr = JSON.parse(res.text).list
    //         var arrDeled = []   
    //         for (var i = 0; i < newArr.length; i++) {
    //           var obj = {}
    //           obj.date = moment(newArr[i].date).format('YYYY-MM-DD')
    //           obj.person = newArr[i].name
    //           obj.status = newArr[i].status
    //           obj.capacity =  newArr[i].capacity
    //           arrDeled.push(obj)
    //         }
    //         this.batteryTableData = arrDeled
    //         var pageNumber = JSON.parse(res.text).totalPage
    //         this.pageTotal = pageNumber
    //         if (pageNumber < 10) {
    //           return
    //         } else {
    //           $('.M-box').pagination({
    //             pageCount: pageNumber,
    //             jump: true,
    //             coping: true,
    //             homePage: '首页',
    //             endPage: '尾页',
    //             prevContent: '«',
    //             nextContent: '»'
    //           })
    //         }
    //       }
    //     })
    // },
    // getRepareRecord (page) {
    //   request
    //     .post('http://192.168.3.52:7099/franchisee/bikeManager/mendRecord?page=' + page)
    //     .send({
    //       'franchiseeId': '123456',
    //       'userId': 'admin',
    //       'code': this.$route.query.code
    //     })
    //     .end((err, res) => {
    //       if (err) {
    //         console.log('err:' + err)
    //       } else {
    //         // console.log(res.text)
    //         // console.log(JSON.parse(res.text).list)
    //         var newArr = JSON.parse(res.text).list
    //         var arrDeled = []
    //         for (var i = 0; i < newArr.length; i++) {
    //           var obj = {}
    //           obj.date = moment(newArr[i].date).format('YYYY-MM-DD')
    //           obj.time = Math.floor((newArr[i].time) / 60000) + ' 分钟'
    //           obj.place = newArr[i].place
    //           obj.person =  newArr[i].name
    //           arrDeled.push(obj)   
    //         }
    //         this.repairTableData = arrDeled
    //         var pageNumber = JSON.parse(res.text).totalPage
    //         this.pageTotal = pageNumber
    //         if (pageNumber < 10) {
    //           return
    //         } else {
    //           $('.M-box').pagination({
    //             pageCount: pageNumber,
    //             jump: true,
    //             coping: true,
    //             homePage: '首页',
    //             endPage: '尾页',
    //             prevContent: '«',
    //             nextContent: '»'
    //           })
    //         }
    //       }
    //     })
    // },
    pageUpdate (e) {
      var that = this
      console.log(this.activeName)
      clearTimeout(this.timer)
      if (e.target.tagName === 'A' || e.target.tagName === 'SPAN') {
        if (e.target.innerHTML === '首页') {
          e.target.innerHTML = 1
        } else if (e.target.innerHTML === '尾页') {
          e.target.innerHTML = this.pageTotal
        } else if (e.target.innerHTML === '«') {
          e.target.innerHTML = Number($('.M-box span.active')[0].innerHTML) - 1
        } else if (e.target.innerHTML === '»') {
          console.log($('.M-box span.active')[0].innerHTML)
          e.target.innerHTML = Number($('.M-box span.active')[0].innerHTML) + 1
        } else if (e.target.innerHTML === '...') {
          return
        }
      } else {
        return
      }
      var type = this.$route.query.type
      this.timer = setTimeout(function () {
        switch (this.activeName) {
          case first:
            that.getBikeEarnings(e.target.innerHTML)
            break
          case second:
            that.getReplaceBatteryRecord(e.target.innerHTML)
          case third:
            that.getRepareRecord(e.target.innerHTML)
        }
      }, 200)
    }
  },
  watch:{
    currentPage3:{
      handler: function(val,oldVal){
      this.loading2 = true
       request.post(host + 'beepartner/franchisee/bike/getBikeDetail')
        .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
      .send({
        code:this.bikeInfo.code,
        currentPage:val,
      })
        .end((error,res)=>{
          if(error){
            console.log(error)
            this.loading2 = false
          }else {
            this.loading2 = false
            this.bikeInfo = Object.assign({},JSON.parse(res.text).bike,{onlineTime:moment(JSON.parse(res.text).bike.onlineTime).format('YYYY-MM-DD')})
            this.tableData = JSON.parse(res.text).data
            this.totalPage = JSON.parse(res.text).totalPage
            this.totalItems = Number(JSON.parse(res.text).totalItems)
              if(this.totalPage>1){
                this.pageShow  = true
              }else {
                this.pageShow = false
              }
            }
        })
      },
      deep: true
    }
  }
}
</script>
<style scoped>
div.el-notification{right:-330px;}
div.carUseDetail {
    background: #fff;
    margin: 0 auto;
    border: 1px solid #e7ecf1;
    width: 1200px;
}

div.carUseDetail table {
  border-collapse: collapse;
  width: 100%;
  padding: 0 30px 0 30px;
}

div.carUseDetail table tr td {
  /* border: 1px solid #f3f3f5; */
  padding: 5px 10px;
}

div.carUseDetail table tr td span.prex {
  display: inline-block;
  width: 80px;
  text-align: right;
  color: #bdb6b6;
  font-size: 14px;
  margin-right: 8px;
}

div.carUseDetail div.detailTitle h3 {
  line-height: 30px;
  /* width: 100%; */
  background: #555;
  color: #fff;
  margin-bottom: 20px;
  padding: 10px;
}

div.carUseDetail div.battery {
  float: right;
}

div.carUseDetail div.battery ul li {
  list-style-type: none;
  margin-bottom: 10px;
}

div.carUseDetail div.battery ul li span.online {
  text-align: center;
  display: block;
  width: 100px;
  padding: 5px 0px;
  background: green;
  color: #fff;
  font-size: 14px;
}

div.carUseDetail div.battery ul li span.lend {
  text-align: center;
  display: block;
  width: 100px;
  padding: 5px 0px;
  background: orange;
  color: #fff;
  font-size: 14px;
}

div.carUseDetail div.battery ul li span.capacity {
  text-align: center;
  display: block;
  width: 100px;
  padding: 5px 0px;
  background: #ff4949;
  color: #fff;
  font-size: 14px;
}

div.carUseDetail div.record {
  margin-top: 25px;
  padding: 10px;
}

div.carUseDetail div.recodeTable table {
  border-collapse: collapse;
  width: 100%;
}

div.carUseDetail div.recodeTable table thead tr th {
  font-weight: normal;
  text-align: left;
  border-bottom: 1px solid #afa7a7;
  padding: 5px 10px;
}

div.carUseDetail div.recodeTable table tbody tr td {
  border: none;
  border-bottom: 1px dotted #afa7a7;
  padding: 10px;
  color: #555;
}

div#carUseDetail_page {
  margin-top: 50px;
}

.el-tabs__active-bar {
  position: absolute;
  width: 0 !important;
  bottom: 0;
  left: 0;
  height: 3px;
  /* background-color: #20a0ff; */
  z-index: 1;
  transition: transform .3s cubic-bezier(.645,.045,.355,1);
  list-style: none;
}

.el-tabs__item.is-active {
  color: #444;
}

.el-tabs__nav-scroll {
    overflow: hidden;
    border-bottom: 2px solid #444;
}

.lang {
  width: 300px;  
}

.help {
  height: 20px;
  width: 20px;
  line-height: 20px;
  cursor: help;
  display: inline-block;
  text-align: center;
  color: #666;
  border-radius: 50%;
  margin-left: 5px;
  border: 1px solid #666;
}
div.el-pagination {margin-left:0;padding-left:0;margin-top:20px;margin-bottom:10px;}
</style>
