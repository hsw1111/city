<template>
  <div class="settleMentPage">
     <div class="hide" v-show="isHide">
          结算已确认，等待财务打款。
          <span class="time"><b>3</b>s</span>
     </div>
    <div class="space">
      <h4>结算单
        <!-- <i class="iconfont icon-guanbi3"></i> -->
      </h4>
      <div class="title">
      <div class="line">
        结算周期：<span>{{month}}</span>
      </div>
      <!-- <div class="line">
        本期总收益：<span>{{new Number(totalProfit).thousandFormat()}}</span>元
      </div> -->
      <div class="line">
        本期结算金额：<span>{{new Number(actProfit).thousandFormat()}}</span>元
      </div>
      <div class="line confirm ">
        <button v-show="isSettled" class="open" @click="openDialog">确定结算</button>
        <el-dialog 
          id="settleMentPage"
          title="结算确认"
          :visible.sync="dialogVisible"
          size="tiny"
          >
          <div>
            <div class="tips">
              结算周期：{{month}}
            </div>
            <div class="tips">
              本期结算金额：<span>{{new Number(actProfitStr).thousandFormat()}}</span>元
            </div>
          </div>
          <span slot="footer" class="dialog-footer">
            
            <el-button type="primary" @click="confirmSubmit">确 定</el-button>
            <el-button @click="dialogVisible = false">取 消</el-button>
          </span>
        </el-dialog>
       
      </div>
      <div class="line line_status " v-show="!isSettled">
        <div v-show="state===3" class="statu rect1"><i>已结算</i></div>
        <div v-show="state===2" class="statu rect2"><i>待结算</i></div>
        <div v-show="state===1" class="statu rect2"><i>待确认</i></div>
      </div>
    </div>
    </div>
    
    <div class="table">
      <h3>
        蜜蜂出行({{$route.query.cityName}})周期费用结算单
      </h3>
      <div class="unit"><span>单位：元</span></div>
        
      <table v-show="type==1">
       
        <thead>
          <tr>
            <th class="dateTime">
              日期
            </th>
             <th class="num">
              有效订单数
            </th>
            <th class="userTotalPayment">
              有效订单总金额
            </th>
            <th class="activeCost">
              <div class="grid_title">活动成本</div>
              <div class="grid">
                <!-- <span title="日期">日期</span> -->
                <span title="优惠券支付">优惠券支付</span>  
                <span title="返赠金额支付">返赠金额支付</span>  
              </div>
            </th>
            <th class="in">
              <div class="grid_title">经营收入</div>
              <div class="grid">
                <!-- <span title="日期">日期</span> -->
                <span title="充值消费" >订单实际收入</span>
                <span title="扣款金额">扣款金额</span>
              </div>
            </th>
            <th class="out">
              <div class="grid_title">经营支出</div>
              <div class="grid">
                 <!-- <div class="item money" title="用户余额退还">用户余额退还</div> -->
                 <div class="item third">
                   <div class="list subtitle">用户缴纳押金支付第三方支付平台服务费</div>
                   <div class="list">
                     <div class="cell" title="缴纳押金/次">缴纳次数</div>
                     <div class="cell" title="押金金额">押金金额</div>
                     <div class="cell" title="服务费率">服务费率</div>
                     <div class="cell" title="支付金额">支付金额</div>
                   </div>
                 </div>
                 <div class="item sevice">
                      <div class="list subtitle">用户消费支付第三方支付平台服务</div>
                      <div class="list">
                        <div class="cell" title="用户消费金额">用户消费金额</div>
                        <div class="cell" title="服务费率">服务费率</div>
                        <div class="cell" title="支付金额">支付金额</div>
                      </div>
                 </div>
                 <div class="item auth">

                   <div class="list subtitle">授权费</div>
                    <div class="list">
                      <div class="cell" title="经营收入">经营收入</div>
                      <div class="cell" title="授权费率">授权费率</div>
                      <div class="cell" title="支付金额">支付金额</div>
                    </div>
                 </div>
              </div>
            </th>
            <th class="count">
              <div class="grid_title">最终收益</div>
              <div class="grid">
                合计
              </div>
            </th>
          </tr> 
        </thead>
        <tbody>
          <tr v-for="list of items" :key="list.id">
           <td class="dateTime">{{list.statisticId}}</td>
            <td class="userTotalPayment">
              {{new Number(list.totalBill).thousand()}}
            </td>
            <td class="userTotalPayment">
              {{new Number(list.totalMoney).thousandFormat()}}
            </td>
            <td class="activeCost" style="text-align:center">
              <div class="grid">
                  <span>{{new Number(list.totalDiscount).thousandFormat()}}</span>
                  <span>{{new Number(list.grantAmountStr).thousandFormat()}}</span>
              </div>
            </td>
           <td class="in">
              <!-- <div class="grid_title">经营收入</div> -->
              <div class="grid">
                <!-- <span>{{list.statisticId}}</span> -->
                <span>{{new Number(list.actualMoneyStr).thousandFormat()}}</span>
                <span>{{new Number(list.decultMoneyStr).thousandFormat()}}</span>
              </div>
            </td>
            <td class="out">
              <!-- <div class="grid_title">经营支出</div> -->
              <div class="grid">
                 <!-- <div class="item money">{{new Number(list.rebackMoneyStr).thousandFormat()}}</div> -->
                 <div class="item third">
                   <!-- <div class="list subtitle">用户缴纳押金支付第三方支付平台服务费</div> -->
                   <div class="list">
                     <div class="cell">{{new Number(list.depositTimes).thousand()}}</div>
                     <div class="cell">{{new Number(list.deposit).thousandFormat()}}</div>
                     <div class="cell">{{list.thirdPartyFeeRate}}</div>
                     <div class="cell">{{new Number(list.thirdFeePayAmt).thousandFormat()}}</div>
                   </div>
                 </div>
                 <div class="item sevice">
                      <!-- <div class="list subtitle">用户消费支付第三方支付平台服务</div> -->
                      <div class="list">
                        <div class="cell">{{new Number(list.actualMoneyStr).thousandFormat()}}</div>
                        <div class="cell">{{list.thirdPartyFeeRate}}</div>
                        <div class="cell">{{new Number(list.payAmtStr).thousandFormat()}}</div>
                      </div>
                 </div>
                 <div class="item auth">

                   <!-- <div class="list subtitle">授权费</div> -->
                    <div class="list">
                      <div class="cell">{{new Number(list.benifitAmt).thousandFormat()}}</div>
                      <div class="cell">{{list.licenseFeeRate}}</div>
                      <div class="cell">{{new Number(list.linceFeePayAmt).thousandFormat()}}</div>
                    </div>
                 </div>
              </div>
            </td>
            <td class="count">
              <!-- <div class="grid_title">最终收益</div> -->
              <div class="grid">
                {{new Number(list.actProfitStr).thousandFormat()}}
              </div>
            </td>
          </tr>
        </tbody>
        <tfoot>
          <tr class="count">
   
             <td data-v-3b262524="" class="dateTime">总计</td>
             <td class="userTotalPayment">
              <!-- {{new Number(list.balanceAmountStr).thousandFormat()}} -->
            </td>
              <td data-v-3b262524="" class="userTotalPayment">
                  {{new Number(sumData.sumTotalMoney).thousandFormat()}}
              </td>
              <td data-v-3b262524="" class="activeCost" style="text-align:center">
                <div data-v-3b262524="" class="grid">
                   <span data-v-3b262524="">{{new Number(sumData.sumTotalDiscount).thousandFormat()}}</span>
                   <span data-v-3b262524="">{{new Number(sumData.sumgrant).thousandFormat()}}</span>
                </div>
              </td>
              <td data-v-3b262524="" class="in">
                  <div data-v-3b262524="" class="grid">
                      <span data-v-3b262524="">{{new Number(sumData.sumactual).thousandFormat()}}</span>
                      <span data-v-3b262524="">{{new Number(sumData.sumdecult).thousandFormat()}}</span>
                  </div>
              </td>
              <td data-v-3b262524="" class="out">
                  <div data-v-3b262524="" class="grid">
                      <div data-v-3b262524="" class="item third">
                          <div data-v-3b262524="" class="list">
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell">{{new Number(sumData.sumdepositpay).thousandFormat()}}</div>
                          </div>
                      </div>
                      <div data-v-3b262524="" class="item sevice">
                          <div data-v-3b262524="" class="list">
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell">{{new Number(sumData.sumthirdpay).thousandFormat()}}</div>
                          </div>
                      </div>
                      <div data-v-3b262524="" class="item auth">
                          <div data-v-3b262524="" class="list">
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell"></div>
                              <div data-v-3b262524="" class="cell">{{new Number(sumData.sumlicensepay).thousandFormat()}}</div>
                          </div>
                      </div>
                  </div>
              </td>
              <td data-v-3b262524="" class="count">
                  <div data-v-3b262524="" class="grid">
                      {{new Number(actProfitStr).thousandFormat()}}
                  </div>
              </td>
          
          </tr>
        </tfoot>
      </table>
      <table v-show="type==2">
        <thead>
          <tr>
            <th class="dateTime">
              日期
            </th>
            <th class="num">
              有效订单数
            </th>
            <th class="userTotalPayment">
              有效订单总金额
            </th>
            <th class="activeCost">
              <div class="grid_title">活动成本</div>
              <div class="grid">
                <!-- <span title="日期">日期</span> -->
                
                 <span title="优惠券支付" >优惠券支付</span>
                 <span title="返赠金额支付" >返赠金额支付</span>
              </div>
            </th>
            <th class="in">
              订单实际收入
            </th>
            <!-- <th class="out">
              <div class="grid_title">经营支出</div>
              <div class="grid">
                 <div class="item third">
                   <div class="list subtitle">用户缴纳押金支付第三方支付平台服务费</div>
                   <div class="list">
                     <div class="cell" title="缴纳押金/次">缴纳押金/次</div>
                     <div class="cell" title="押金金额">押金金额</div>
                     <div class="cell" title="服务费率">服务费率</div>
                     <div class="cell" title="支付金额">支付金额</div>
                   </div>
                 </div>
                 <div class="item sevice">
                      <div class="list subtitle">用户消费支付第三方支付平台服务</div>
                      <div class="list">
                        <div class="cell" title="用户消费金额">用户消费金额</div>
                        <div class="cell" title="服务费率">服务费率</div>
                        <div class="cell" title="支付金额">支付金额</div>
                      </div>
                 </div>
                 <div class="item auth">

                   <div class="list subtitle">运营管理费</div>
                    <div class="list">
                      <div class="cell" title="车辆数">车辆数</div>
                      <div class="cell" title="每车每日管理费">每车每日管理费</div>
                      <div class="cell" title="支付金额">支付金额</div>
                    </div>
                 </div>
              </div>
            </th> -->
            <th class="managefee">
              <div class="fee_title">运营管理费</div>
              <div class="fee">
                <span title="车辆数">车辆数</span>
                <span title="每车每日管理费">每车每日管理费</span>
                <span title="支付金额">支付金额</span>
              </div>
            </th>
            <th class="count">
              <div class="grid_title">最终收益</div>
              <div class="grid">
                合计
              </div>
            </th>
          </tr> 
        </thead>
        <tbody>
          <tr v-for="list of items" :key="list.id">
           <td class="dateTime">{{list.statisticId}}</td>
            <td class="userTotalPayment">
              {{new Number(list.totalBill).thousand()}}
            </td>
            <td class="userTotalPayment">
              {{new Number(list.totalMoney).thousandFormat()}}
            </td>
            <td class="activeCost" style="text-align:center">
              <div class="grid">
                <span>{{new Number(list.totalDiscount).thousandFormat()}}</span>
                <span>{{new Number(list.grantAmountStr).thousandFormat()}}</span>
              </div>
            </td>
           <td class="userTotalPayment">
              {{new Number(list.actualMoneyStr).thousandFormat()}}
            </td>

            <td class="managefee">
              <div class="line">
                <span>{{new Number(list.bikeNum).thousand()}}</span>
                <span>{{new Number(list.manageFee).thousandFormat()}}</span>
                <span>{{new Number(list.managePayAmtStr).thousandFormat()}}</span>
              </div>
            </td>
            <td class="count">
              <!-- <div class="grid_title">最终收益</div> -->
              <div class="grid">
                {{new Number(list.actProfitStr).thousandFormat()}}
              </div>
            </td>
          </tr>
        </tbody>
        <tfoot>
          <tr class="count">

             <td data-v-3b262524="" class="dateTime">总计</td>
              <td data-v-3b262524="" class="userTotalPayment">
                  <!-- {{new Number(sumData.sumbalance).thousandFormat()}} -->
              </td>
              <td data-v-3b262524="" class="userTotalPayment">
                  {{new Number(sumData.sumTotalMoney).thousandFormat()}}
              </td>
              <td data-v-3b262524="" class="activeCost" style="text-align:center">
                <div data-v-3b262524="" class="grid">
                      <span data-v-3b262524=""> {{new Number(sumData.sumTotalDiscount).thousandFormat()}}</span>
                      <span data-v-3b262524=""> {{new Number(sumData.sumgrant).thousandFormat()}}</span>
                </div>
                 
              </td>
              <td data-v-3b262524="" class="userTotalPayment">
                {{new Number(sumData.sumactual).thousandFormat()}}
              </td>
             
            <td class="managefee">
              <div class="line">
                <span></span>
                <span></span>
                <span>{{new Number(sumData.summanagepay).thousandFormat()}}</span>
              </div>
            </td>
              <td data-v-3b262524="" class="count">
                  <div data-v-3b262524="" class="grid">
                      {{new Number(actProfitStr).thousandFormat()}}
                  </div>
              </td>
           
          </tr>
        </tfoot>
      </table>
      <table class="special" v-show="type==3">
        <thead>
          <tr>
            <th class="dateTime">
              日期
            </th>
             <th class="num">
              有效订单数
            </th>
            <th class="userTotalPayment">
              有效订单总金额
            </th>
            <th class="activeCost">
              <div class="grid_title">活动成本</div>
              <div class="grid">
                <span title="优惠券支付" >优惠券支付</span>
                <span title="返赠金额支付">返赠金额支付</span>

              </div>
            </th>
            <th class="in">
              订单实际收入
            </th>
            <!-- <th class="out">
              <div class="grid_title">经营支出</div>
              <div class="grid">
                 <div class="item third">
                   <div class="list subtitle">用户缴纳押金支付第三方支付平台服务费</div>
                   <div class="list">
                     <div class="cell" title="缴纳押金/次">缴纳押金/次</div>
                     <div class="cell" title="押金金额">押金金额</div>
                     <div class="cell" title="服务费率">服务费率</div>
                     <div class="cell" title="支付金额">支付金额</div>
                   </div>
                 </div>
                 <div class="item sevice">
                      <div class="list subtitle">用户消费支付第三方支付平台服务</div>
                      <div class="list">
                        <div class="cell" title="用户消费金额">用户消费金额</div>
                        <div class="cell" title="服务费率">服务费率</div>
                        <div class="cell" title="支付金额">支付金额</div>
                      </div>
                 </div>
                
              </div>
            </th> -->
            <th class="matchRate">
              分成比例
            </th>
            <th class="count">
              <div class="grid_title">最终收益</div>
              <div class="grid">
                合计
              </div>
            </th>
          </tr> 
        </thead>
        <tbody>
          <tr v-for="list of items" :key="list.id">
           <td class="dateTime">{{list.statisticId}}</td>
            <td class="userTotalPayment">
              {{new Number(list.totalBill).thousand()}}
            </td>
            <td class="userTotalPayment">
              {{new Number(list.totalMoney).thousandFormat()}}
            </td>
            <td class="activeCost" style="text-align:center">
             
              <div class="grid">
                <span> {{new Number(list.totalDiscount).thousandFormat()}}</span>
                <span> {{new Number(list.grantAmountStr).thousandFormat()}}</span>
              </div>
            </td>
           <td class="userTotalPayment">
             {{new Number(list.actualMoneyStr).thousandFormat()}}
            </td>
           
            <td class="matchRate">
              {{new Number(list.divisionPercentStr).thousandFormat()}}
            </td>
            <td class="count">
              <!-- <div class="grid_title">最终收益</div> -->
              <div class="grid">
                {{new Number(list.actProfitStr).thousandFormat()}}
              </div>
            </td>
          </tr>
        </tbody>
        <tfoot>
          <tr class="count">

            <td data-v-3b262524="" class="dateTime">总计</td>
            <td data-v-3b262524="" class="userTotalPayment">
               <!-- {{new Number(sumData.sumbalance).thousandFormat()}} -->
            </td>
            <td data-v-3b262524="" class="userTotalPayment">
               {{new Number(sumData.sumTotalMoney).thousandFormat()}}
            </td>
            <td data-v-3b262524="" class="activeCost" style="text-align:center">
                
                <div data-v-3b262524="" class="grid">
                    <span data-v-3b262524="">{{new Number(sumData.sumTotalDiscount).thousandFormat()}}</span>
                    <span data-v-3b262524="">{{new Number(sumData.sumgrant).thousandFormat()}}</span>
                </div>
            </td>
            <td data-v-3b262524="" class="userTotalPayment">
              {{new Number(sumData.sumactual).thousandFormat()}}
            </td>
           
            <td class="matchRate">
              
            </td>
            <td data-v-3b262524="" class="count">
                <div data-v-3b262524="" class="grid">
                    {{new Number(actProfitStr).thousandFormat()}}
                </div>
            </td>

          </tr>
        </tfoot>
      </table>
    </div>
    <div class="tips" v-show="type==1">
      <ul>
        <li>1、经营收入总和减去经营支出总和等于最终收益。       </li>
        <li>2、用户缴纳押金支付第三方支付平台服务费是根据每日用户缴纳押金次数乘以押金金额再乘以千分之六。          </li>
        <li>3、用户消费支付第三方支付平台服务费是根据消费金额乘以千分之六。          </li>
        <li>4、授权费为北京蜜蜂出行科技有限公司收取的授权经营服务费，根据协议签订费率计算。          </li>
        <li>5、加盟商审核后点击“确定结算”按钮后生效，等待蜜蜂财务打款。          </li>
      </ul>
    </div>
    <div class="tips" v-show="type==2">
      <ul>
        <li>1、订单实际收入总和减去运营管理费总和等于最终收益。       </li>
        <li>2、运营管理费为北京蜜蜂出行科技有限公司收取的运营管理费用，根据协议签订费率计算。          </li>
        <li>3、加盟商审核后点击“确定结算”按钮后生效，等待蜜蜂财务打款。          </li>
      </ul>
    </div>
    <div class="tips" v-show="type==3">
      <ul>
        <li>1、订单实际收入总和乘以分成比例等于最终收益。       </li>
        <li>2、分成比例为加盟商与北京蜜蜂出行科技有限公司协议签订的值。          </li>
        <li>3、加盟商审核后点击“确定结算”按钮后生效，等待蜜蜂财务打款。           </li>
      </ul>
    </div>

  </div>
</template>
<script>
// import $ from 'jquery'
import request from 'superagent'
import { host } from '../../../config/index'
import {mapGetters,mapActions} from 'vuex'
import {thousandFormat} from '../../../util/util.js'
  export default {
    computed:{
      ...mapGetters(['settelListId','confirmRecord'])
    },
    data(){
      return {
        sumData:"",
        type:'3',
        list: [],
        state:'',
        actProfit:'',
        actProfitStr: '',
        totalProfit:'',
        status:false,
        isSettled:false,
        dialogVisible:false,
        isHide:false,
        month:'',
        wType: '',
        cityId: '',
        items:[
        ]
      }
    },
     mounted(){
        
       document.title="结算单"
        this.month = this.$route.query.month
        this.wType = this.$route.query.wType
        this.cityId = this.$route.query.cityId
       request
      .post(host + 'beepartner/franchisee/withDraw/getWithDrawRecordDetail')
      .withCredentials()
      .set({
        'content-type': 'application/x-www-form-urlencoded'
      })
      .send({
        applyTimeStr:this.month,
        wType:this.wType,
        cityId:this.cityId
      })
      .end((err, res) => {
        if (err) {
          console.log('err:' + err)
         
        } else {
          // loading 关闭
          var code = JSON.parse(res.text).resultCode
          var message = JSON.parse(res.text).message
          this.state = JSON.parse(res.text).withDrawRecord.status
          this.actProfit = JSON.parse(res.text).withDrawRecord.actProfit
          if(this.state===1){
            this.isSettled = true
          }else if(this.state===2||this.actProfit===0){
            this.isSettled = false
          }else{
             this.isSettled = false
          }
          if(code  === -1){
             //this.$router.push('/login')
          }
          if (code === 1) {
            var newArr = JSON.parse(res.text).data
            this.items = newArr
            console.log(newArr)
            console.log(JSON.parse(res.text))
            this.totalProfit =  JSON.parse(res.text).withDrawRecord.totalProfit
            this.actProfit = JSON.parse(res.text).withDrawRecord.actProfit
            this.actProfitStr = JSON.parse(res.text).withDrawRecord.totalProfit
            // 判断生成的结算单类型
            this.type = JSON.parse(res.text).withDrawRecord.tbType
            // 表格最后一行总计
            this.sumData = JSON.parse(res.text).withDrawRecord
            console.log(this.sumData)

           
          }
        }
       
      })

        
       
    },
    methods:{
      ...mapActions(['setConfirmRecord']),
      openDialog(){
        this.dialogVisible = true;
      },
      confirmSubmit(){
        var that = this;
        this.dialogVisible = false;
        this.isHide = true
        var text = $('span.time').find('b').text();
        var timer = setInterval(()=>{
          text--
          $('span.time').find('b').text(text)
          if(text==0){
            clearInterval(timer)
           this.isHide = false
            $('span.time').find('b').text('3')
          }
        },1000)
        // 发起结算请求
        setTimeout(function(){
             request
          .post(host + 'beepartner/franchisee/withDraw/applyWithDrawMoney')
          .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
          .send({
            applyTimeStr:that.month,
            wType:that.wType,
            cityId:that.cityId
          })
          .end((err,res)=>{
            if(err){
              console.log(err)
            }else{
              console.log(res)

              var code = JSON.parse(res.text).resultCode
              var message = JSON.parse(res.text).message
              if(code===1){
                that.$message({
                  message:message,
                  type:'success'
                })
                that.isSettled = false
                that.status = true
                // 结算成功修改vuex中的值为true
                // that.setConfirmRecord(true)
                const io = require('socket.io-client');
                const ws = io.connect("http://47.94.39.104:3000")
                ws.emit('join',{type:'detail'})
                ws.on('broadcast_join', function (data) {
                      console.log(data.type);
                  });
              }
              if(code == 0 ){
                that.$message({
                  message:message,
                  type:'error'
                })
              }
            }
          })
        },3000)
       
       
      }
    }
  }
</script>
<style lang="scss" scoped>
 th.num {background:yellowgreen}
  div.settleMentPage div.title div.line.confirm{float:right;}

  th.managefee {
     background:#ebf1dc
  }
 div.fee_title {
   border-bottom: 1px solid #555;
    height: 30px;
    line-height: 30px;
   
 }
 .managefee .fee span {
   width: calc(100% / 3);
    height: 40px;
    line-height: 40px;
    text-align: center;
    float: left;
    box-sizing: border-box;
    border-right: 1px solid #555;
    overflow: hidden;
    white-space: nowrap;
 }
 .managefee .fee span:nth-child(3){
   border-right:none
 }
  .managefee .line span {
   width: calc(100% / 3);
    height: 30px;
    line-height: 30px;
    text-align: center;
    float: left;
    box-sizing: border-box;
    border-right: 1px solid #555;
    overflow: hidden;
    white-space: nowrap;
 }
 .managefee .line span:nth-child(3){
   border-right:none
 }
  div.hide{
    height: 30px;
    width: 200px;
    background: #ffffff;
    color: #555;
    border: 1px solid rgb(153, 153, 153);
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 70px;
    margin-left: -100px;
    margin-top: -15px;
    padding: 15px;
    font-size: 14px;
    line-height: 30px;
  }
  div.settleMentPage{
    margin: 0 auto;
    box-sizing: border-box;
    border: 1px solid #e7ecf1;
    div.space{
       background: #555;
       padding-bottom:10px;
    }
    h4{
      line-height:50px;
      text-indent: 10px;
      color: #fff;
      font-size: 20px;
      i{float:right;padding-right:10px;cursor:pointer;color:#b1abab;transition:all linear .5s;
        &:hover{
          transform: rotate(180deg)
        }
      }
    }
    div.title{
        height:70px;line-height:70px;
        padding-left: 10px;
        background:#fff;
        color:#000;
        overflow: hidden;
      div.line{
        float:left;
        margin-right:20px;
        font-size:16px;
        button.open{
          border:none;
          outline: none;
          border:1px solid orange;
          background:orange;
          color:#fff;
          border-radius: 5px;
          width:120px;
          cursor:pointer;
          padding:10px;
          margin-left:50px;
        }
      }
      div.line_status{
        float:right;
        div.statu{
          display: inline-block;
          transform:skew(-20deg);
          color:#fff;
          width:80px;
          height: 30px;
          line-height: 30px;
          text-align:center;
          margin-right:10px;
          i{
             transform:skew(20deg);
             font-style: normal;
             display: block;
          }
        }
        div.rect1{
          background:#009900;
        }
        div.rect2{
          background:orange;
        }
      }
    }
    $tableBorderColor:1px solid #555;
    div.table{
      padding:0 10px;
      margin-top:20px;
      h3{margin:25px auto;text-align:center;}
      div.unit{
        text-align: right;
        font-size: 12px;
        font-weight: bold;
        padding-bottom: 6px;
        span{margin-right: 20px;}
      }
     table.special{
       thead{
         tr{
           th{
             div.grid{
               div.third,div.sevice{width:calc(100% / 2)}
               div.sevice{border-right:none;}
             }
           }
           th.matchRate{
             background:rgb(255, 190, 0);
           }
         }
       }
       tbody{
         tr{
           td{
             div.grid{
               div.third,div.sevice{width:calc(100% / 2)}
               div.sevice{border-right:none;}
             }
           }
           td.activeCost{text-align:center;}
           td.matchRate{text-align:center;}
         }
       }
        tfoot{
         tr{
           td{
             div.grid{
               div.third,div.sevice{width:calc(100% / 2)}
               div.sevice{border-right:none;}
             }
           }
           td.activeCost{text-align:center;}
           td.matchRate{text-align:center;}
         }
       }
     }
     table{
       width:100%;
       border-collapse: collapse;
        thead{
          tr{
            th.in{width:auto;background:#dde7f0;}
            th{border:$tableBorderColor;
              font-size:12px;
              div.grid_title{border-bottom:$tableBorderColor;height:30px;line-height: 30px;}
              div.grid{
                span{
                  width:calc(100% / 2);
                   height: 40px;
                  line-height: 40px;
                  text-align: center;
                  float:left;
                 
                  box-sizing: border-box;
                  border-right:$tableBorderColor;
                  overflow: hidden;
                 
                  white-space: nowrap;
                  &:nth-last-child(1){
                    border-right:none;
                  }
                }
                div.item{
                  border-right:$tableBorderColor;
                  float:left;
                  height:40px;
                  border-bottom:none;
                  box-sizing:border-box;
                  div.list{display: block;
                    div.cell{float:left;width:25%;box-sizing:border-box;border-right:$tableBorderColor;height:20px;line-height:20px;}
                    div.cell{
                      &:nth-last-child(1){border-right:none;text-align: center;}
                    }
                  }
                  div.subtitle{ border-bottom:$tableBorderColor;height:20px;line-height:20px;}
                }
                div.sevice{
                   width:calc(100% / 3);
                  div.list{
                    div.cell{width:calc(100% / 3)}
                  }
                }
                 div.auth{
                 width:calc(100% / 3);
                  border-right:none;
                  div.list{
                    div.cell{width:calc(100% / 3)}
                  }
                }
                div.money{
                   width:9%;
                   line-height:40px;
                   text-align: center;
                  
                }
                div.third{width:calc( 100% / 3);}
              }
            }
            th.activeCost{
              background:#ffe9d5;
              div.grid{
                height:40px;line-height:40px;
              }
            }
            th.count{
              background:#ffff24;
              div.grid{height:40px;line-height:40px;}
            }
            th.out{background:#ebf1dc;}
            th.dateTime{background:yellow}
            th.userTotalPayment{background:skyblue}
          }
        }
        tfoot,tbody{
          tr{
            td.dateTime{text-align:center;}
            td.userTotalPayment{text-align:center;}
            td.in{width:auto;}
            td{border:$tableBorderColor;
              font-size:12px;
              div.grid_title{border-bottom:$tableBorderColor;}
              div.grid{
                span{
                   height: 30px;
                  line-height: 30px;
                  width:calc(100% / 2);
                  text-align: center;
                  float:left;
                  // padding:0 10px;
                  box-sizing: border-box;
                  border-right:$tableBorderColor;
                  &:nth-last-child(1){
                    border-right:none;
                  }
                }
                div.item{
                  border-right:$tableBorderColor;
                  float:left;
                  height:30px;
                  border-bottom:none;
                  box-sizing:border-box;
                  div.list{display: block;
                    div.cell{text-align:center;float:left;padding:0 10px;width:25%;box-sizing:border-box;border-right:$tableBorderColor;height:30px;line-height:30px;}
                    div.cell{
                      &:nth-last-child(1){border-right:none;text-align: center;}
                    }
                  }
                  div.subtitle{ border-bottom:$tableBorderColor;height:20px;line-height:20px;}
                }
                div.sevice{
                 width:calc(100% / 3);
                  div.list{
                    div.cell{width:calc(100% / 3)}
                  }
                }
                 div.auth{
                  width:calc(100% / 3);
                  border-right:none;
                  div.list{
                    div.cell{width:calc(100% / 3)}
                  }
                }
                div.money{
                   width:9%;
                   line-height:30px;
                    text-align: center;
                }
                div.third{width:calc(100% / 3);}
              }
            }
            td.count{
              div.grid{height:30px;line-height:30px;text-align:center;}
            }
          }
        }
        tfoot{
          tr{
            border:$tableBorderColor;
           th.totalText{text-align:right;border-right:$tableBorderColor;} 
           td{font-weight:bold;}
          }
        }
     }
    }
    div.tips{
      margin-top:20px;
       padding:0 10px;
      ul{
        margin-top:30px;
        li{margin-bottom: 3px;
    list-style: none;
    color: #d2cece;
    font-size: 12px;}
      }
    }
  }
  div#settleMentPage .dialog-footer button:nth-child(1){width:88px;background:#666666;outline:none;border:1px solid #666666;}
  div#settleMentPage .dialog-footer button:nth-child(2){width:88px;background:#fff;outline:none;border:1px solid #666666;
    &:hover{color:#666;}
  }
  
</style>