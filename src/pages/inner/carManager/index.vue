<template>
  <div class="carManager" style="margin-right: 20px;">
    <div v-title data-title="车辆管理"></div> 
    <div class="carManager_content">
      <div class="queryCarInfo">
        <el-form :model="form">
          <el-row  v-show="remoteCityList.length>1">
             <el-form-item style=" margin-bottom: -7px;">
                <span class="labelAlign" style="margin-top:3px">加盟区域</span>
               <city-list v-bind:joinCity="remoteCityList" v-on:listenToChildEvetn="showMsgFormChild"></city-list>
               
              </el-form-item>
          </el-row>
          <el-row>
            <el-col>
              <el-form-item class="filtercar el-col-12">
                <span class="labelAlign">关键字</span>
                <!-- <input v-model="terminalNumber" v-on:input='inputChange' class="carMan_bar" placeholder="车辆号\终端编号"> -->
                <input v-model="terminalNumber"  class="carMan_bar" placeholder="车辆号\终端编号">
              </el-form-item>
              <el-form-item class="filtercar el-col-12">
                <span class="labelAlign">运营状态</span>
                <el-checkbox-group v-model="checkList" style="width: 400px;">
                  <el-checkbox label="4">待出租</el-checkbox>
                  <el-checkbox label="5">已预订</el-checkbox>
                  <el-checkbox label="6">已出租</el-checkbox>
                  <el-checkbox label="7">维护中</el-checkbox>
                </el-checkbox-group>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row style="margin-bottom: 3px;">
            <el-col>
              <el-form-item>
                <span class="labelAlign">上线日期</span>
                <el-date-picker v-model='form.data1' type="date" placeholder="选择日期"></el-date-picker>
                <span class="division">至</span>
                <el-date-picker v-model='form.data2' type="date" placeholder="选择日期"></el-date-picker>
                <input type="button" class="my_btn" @click="searchByTimeline" style="font-size: 14px;line-height:36px" value="查询">
                <!--<button @click='searchByTimeline'>查询</button>-->
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </div>
    </div>
    <div class="showCarInfo">
  
      <el-table :data="tableData" style="width: 100% font-size:13px; color: #6c6c6c;" v-loading="loading2" element-loading-text="拼命加载中" :empty-text='emptyText'>
        <el-table-column min-width="30" label="车辆号" prop='bikeCode'>
          <template slot-scope="scope">
            <!-- <a>{{scope.row.bikeCode}}</a> -->
            <router-link style="color:rgb(118, 103, 233); text-decoration: none;" target='_blank' v-bind:to="{path:'/index/carManager/carUseDetail', query: {code:scope.row.bikeCode}}">{{scope.row.bikeCode}}</router-link>
            <!-- <a @click="$router.push({path:'/carUseDetail', query: {code:scope.row.bikeCode}})">{{scope.row.bikeCode}}</a>  -->
          </template>
        </el-table-column>
        <el-table-column prop="boxCode" label="终端编号" min-width="50">
        </el-table-column>
        <el-table-column prop="onlineTime" label="上线日期" min-width="50">
        </el-table-column>
        <el-table-column prop="stateName" label="运营状态" min-width="50">
        </el-table-column>
        <el-table-column prop="location" label="车辆位置">
        </el-table-column>
      </el-table>
    </div>
    <div style="margin-top: -20px;border: 1px solid #e7ecf1;background: #fff;border-top: none;border-bottom: none; padding-bottom: 10px;padding-left: 20px;">
      <el-pagination v-show="pageShow" @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page.sync="currentPage3" :page-size="10" layout="prev, pager, next, jumper" :total="totalItems">
      </el-pagination>
    </div>
    <!-- <div id="carManager_page">
        <div class="M-box"></div>
      </div> -->
  </div>
</template>
<script>
import cityList from '../../../components/cityList.vue'
import request from 'superagent'
// import moment from 'moment'
// import $ from 'jquery'
// import Vue from 'vue'
import { host } from '../../../config/index.js'
export default {
  data: function () {
    return {
      cityCodeList:[],
      remoteCityList:[],
      emptyText: ' ',
      form: {
        radio: '',
        data1: '',
        data2: ''
      },
      isQuery: false,
      totalItems: 1,
      pageShow: false,
      currentPage3: 1,
      tableData: [],
      timer: null,
      checkList: [],
      pagetotal: '',
      terminalNumber: '',
      noDate: false,
      loading2: false,
      isSearch: false
    }
  },
  created() {
    // 初始化调用查询可加盟城市的接口,动态渲染数据
    var that = this;
    request
      .post(host + "beepartner/franchisee/city/findCitysByCityPartner")
      .withCredentials()
      .set({
        "content-type": "application/x-www-form-urlencoded"
      })
      .end((error, res) => {
        if (error) {
          console.log(error);
        } else {
          var result = JSON.parse(res.text);
          if(result.data==null){
            return
          }else{
            var arr = result.data.map(list => {
                return { cityName: list.cityName, code: list.cityId, id: list.id };
              });
            
              this.remoteCityList = arr
          }
         
         
        
        }
      });
  
  },
  components:{
    cityList
  },
  mounted: function () {
     var cityId = this.$route.query.cityId
      setTimeout(()=>{
         this.remoteCityList.map((item)=>{
         if(item.code == cityId){
           var cityName = item.cityName
           $('.cityList ul li').each(function(index,item){
             var text = $(item).text()
             if(cityName==text){
              $(item).click()
             }
           })
         }
       })
      },200)
    // this.mountedWay()
    $(".sign").removeClass('is-active')
    $('.sign[name="1200"]').addClass('is-active')
  },
  methods: {
     showMsgFormChild(data){
      // 子组件像父组件传值,目的是获取被选中的cityCode
      this.cityCodeList = data
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    },
    handleList() {
      // this.searchByTimeline()
      var radio = this.checkList.toString()
      var startTime, endTime
      // if (this.form.data1 === '' || this.form.data2 === '') {
      //   startTime = ''
      //   endTime = ''
      // } else {
      //   startTime = moment(this.form.data1).format('YYYY-MM-DD')
      //   endTime = moment(this.form.data2).format('YYYY-MM-DD')
      // }
       startTime = this.form.data1 == ''?"":moment(this.form.data1).format('YYYY-MM-DD')
       endTime = this.form.data2 === ''?"":moment(this.form.data2).format('YYYY-MM-DD')

       var _startTime = new Date(this.form.data1).getTime()
        var _endTime = new Date(this.form.data2).getTime()
        _endTime = isNaN(_endTime) ? 0 : _endTime
        _startTime = isNaN(_startTime) ? 0 : _startTime
         if(_startTime!="" &&_endTime!=""&&_endTime<_startTime){
              this.$message({
                type: 'warning',
                message: '开始日期不能大于结束日期'
              })
              return
        }

      //    if (_endTime > 1 && _startTime <= 1) {
      //       this.$message({
      //         type: 'warning',
      //         message: '开始日期不能为空'
      //       })
      //       return
      //     } 
      //   if (_endTime < 0) {
      //       this.$message({
      //         type: 'warning',
      //         message: '结束日期不能为空'
      //       })
      //     } else {
      //       if(_endTime<_startTime){
      //         this.$message({
      //           type: 'warning',
      //           message: '开始日期不能大于结束日期'
      //         })
      //         return
      //       }
      //     }
          // console.log(_startTime,_endTime)
      // 根据用户选择不同状态进行数据的筛选
      this.isQuery = true
      if (this.checkList.length > 0) {
        this.loading2 = true
        this.emptyText = ' '
        request
          .post(host + 'beepartner/franchisee/bike/findBike')
          .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
          .send({
            cityId:this.cityCodeList.join(),
            'startOnlineTime': startTime,
            'endOnlineTime': endTime,
            'bikeState': radio,
            'keyName': this.terminalNumber,
            currentPage:this.currentPage3
          })
          .end((error, res) => {
            if (error) {
              console.log('error:', error)
               this.loading2 = false
               this.emptyText = '暂无数据'
            } else {
               this.loading2 = false
              var data = (JSON.parse(res.text)).data
              var newData = this.tableDataDel(data)
              this.pagetotal = (JSON.parse(res.text)).totalPage
              if (this.pagetotal > 1) {
                this.pageShow = true
              } else {
                this.pageShow = false
                this.emptyText = ' 暂无数据'
              }
              this.totalItems = Number(JSON.parse((JSON.parse(res.text)).totalItems))
              // loading 关闭
              this.loading2 = false
              this.tableData = newData
              this.currentPage3 = 1
            }
          })
      } else {
        this.isQuery = false
        this.currentPage3 = 1
         this.loading2 = true
        request
          .post(host + 'beepartner/franchisee/bike/findBike')
          .withCredentials()
          .set({
            'content-type': 'application/x-www-form-urlencoded'
          })
          .send({
            cityId:this.cityCodeList.join(),
            'startOnlineTime': startTime,
            'endOnlineTime': endTime,
            'bikeState': radio,
            'keyName': this.terminalNumber,
            currentPage:this.currentPage3
          })
          .end((error, res) => {
            if (error) {
              this.loading2 = false
               this.pageShow = false
              this.emptyText = '暂无数据'
              console.log('error:', error)
               this.loading2 = false
            } else {
               this.loading2 = false
              var data = JSON.parse(res.text).data
              var newData = this.tableDataDel(data)
              this.pagetotal = (JSON.parse(res.text)).totalPage
              this.tableData = newData
              this.totalItems = Number((JSON.parse(res.text)).totalItems)
              // loading 关闭
              this.loading2 = false
              if (this.pagetotal > 1) {
                this.pageShow = true
              } else {
                this.pageShow = false
                this.emptyText = '暂无数据'
                return
              }
            }
          })
      }

    },
    searchByTimeline() {
      this.currentPage3 = 1
      this.isSearch = true
      var that = this
      // if (this.terminalNumber === '' && this.form.data1 === '' && this.form.data2 === '') {
      //   this.$message({
      //     message: '请输入查询条件',
      //     type: 'warning'
      //   })   
      // } else {
        this.isQuery = true
        var startTime, endTime
        // if (this.form.data1 === '' || this.form.data2 === '') {
        //   startTime = null
        //   endTime = null
        // } else {
        //   startTime = moment(this.form.data1).format('YYYY-MM-DD')
        //   endTime = moment(this.form.data2).format('YYYY-MM-DD')
        // }
         startTime = this.form.data1 == ''?"":moment(this.form.data1).format('YYYY-MM-DD')
        endTime = this.form.data2 === ''?"":moment(this.form.data2).format('YYYY-MM-DD')
        var radio = this.checkList.toString()
        var _startTime = new Date(this.form.data1).getTime()
        var _endTime = new Date(this.form.data2).getTime()
        _endTime = isNaN(_endTime) ? 0 : _endTime
        _startTime = isNaN(_startTime) ? 0 : _startTime
        if(_startTime!="" &&_endTime!=""&&_endTime<_startTime){
              this.$message({
                type: 'warning',
                message: '开始日期不能大于结束日期'
              })
              return
        }
        // console.log(_endTime, _startTime)
        // if (_endTime > _startTime) {
        //   if (_endTime > 1 && _startTime <= 1) {
        //     this.$message({
        //       type: 'warning',
        //       message: '开始日期不能为空'
        //     })
        //   } else {
            this.loading2 = true
            request
              .post(host + 'beepartner/franchisee/bike/findBike')
              .withCredentials()
              .set({
                'content-type': 'application/x-www-form-urlencoded'
              })
              .send({
                cityId:this.cityCodeList.join(),
                'startOnlineTime': startTime,
                'endOnlineTime': endTime,
                'bikeState': radio,
                'keyName': this.terminalNumber,
                currentPage:this.currentPage3
              })
              .end((error, res) => {
                if (error) {
                  this.loading2 = false
                  this.emptyText = '暂无数据'
                  console.log('error:', error)
                } else {
                  var data = JSON.parse(res.text).data
                  var newData = this.tableDataDel(data)
                  this.pagetotal = (JSON.parse(res.text)).totalPage
                  this.tableData = newData
                  this.totalItems = Number(JSON.parse((JSON.parse(res.text)).totalItems))
                  // loading 关闭
                  this.loading2 = false
                  if (this.pagetotal > 1) {
                    this.pageShow = true
                  } else {
                    this.emptyText = '暂无数据'
                    this.pageShow = false
                    return
                  }
                }
              })
          // }
        // } else {
        //     if(_endTime<_startTime){
        //       this.$message({
        //         type: 'warning',
        //         message: '开始日期不能大于结束日期'
        //       })
        //       return
        //     }
        //     request
        //       .post(host + 'beepartner/franchisee/bike/findBike')
        //       .withCredentials()
        //       .set({
        //         'content-type': 'application/x-www-form-urlencoded'
        //       })
        //       .send({
        //         cityId:this.cityCodeList.join(),
        //         'startOnlineTime': startTime,
        //         'endOnlineTime': endTime,
        //         'bikeState': radio,
        //         'keyName': this.terminalNumber,
        //         currentPage:this.currentPage3
        //       })
        //       .end((error, res) => {
        //         if (error) {
        //           this.loading2 = false
        //           this.emptyText = '暂无数据'
        //           console.log('error:', error)
        //         } else {
        //           var data = JSON.parse(res.text).data
        //           var newData = this.tableDataDel(data)
        //           this.pagetotal = (JSON.parse(res.text)).totalPage
        //           this.tableData = newData
        //           this.totalItems = Number((JSON.parse(res.text)).totalItems)
        //           // loading 关闭
        //           this.loading2 = false
        //           if (this.pagetotal > 1) {
        //             this.pageShow = true
        //           } else {
        //             this.emptyText = '暂无数据'
        //             this.pageShow = false
        //             return
        //           }
        //         }
        //       })
          

        // }
        // return
      // }
    },
    tableDataDel(arr) {
      var arrDeled = []
      if(arr!=null){
          for (var i = 0; i < arr.length; i++) {
          var obj = {}
          obj.bikeCode = arr[i].code
          obj.boxCode = arr[i].boxCode
          obj.onlineTime = arr[i].onlineTimeStr
          obj.stateName = arr[i].stateName
          obj.location = arr[i].location

          arrDeled.push(obj)
        }
      }
      

      // console.log('arrDeled:', arrDeled)
      return arrDeled
    },
    // inputChange() {
    //   if (this.form.data1 === '' && this.form.data2 === '' && this.terminalNumber === '' && this.checkList.length === 0) {
    //     this.isQuery = false
    //     this.isSearch = false
    //     this.mountedWay()
    //   } else {  
    //     this.isQuery = true
    //     var startTime = null
    //     var endTime = null
    //     if (this.form.data1 === '' || this.form.data2 === '') {
    //       startTime = ''
    //       endTime = ''
    //     } else {
    //       startTime = moment(this.form.data1).format('YYYY-MM-DD')
    //       endTime = moment(this.form.data2).format('YYYY-MM-DD')
    //     }

    //     var radio = this.checkList.toString()
    //     return
    //     request
    //       .post(host + 'beepartner/franchisee/bike/findBike')
    //       .withCredentials()
    //       .set({
    //         'content-type': 'application/x-www-form-urlencoded'
    //       })
    //       .send({
    //         cityId:this.cityCodeList.join(),
    //         'startOnlineTime': startTime,
    //         'endOnlineTime': endTime,
    //         'bikeState': radio,
    //         'keyName': this.terminalNumber,
    //         currentPage:this.currentPage3
    //       })
    //       .end((error, res) => {
    //         if (error) {
    //           console.log('error:', error)
    //         } else {
    //           console.log(JSON.parse(res.text).data)
    //           var data = (JSON.parse(res.text)).data
    //           var newData = this.tableDataDel(data)
    //           this.pagetotal = (JSON.parse(res.text)).totalPage
    //           this.totalItems = Number(JSON.parse((JSON.parse(res.text)).totalItems))
    //           // loading 关闭
    //           this.loading2 = false
    //           this.tableData = newData
    //           if (this.pagetotal > 1) {
    //             this.pageShow = true
    //           } else {
    //             this.pageShow = false
    //             return
    //           }
    //         }
    //       })
    //     return
    //   }
    // },
    mountedWay() {
      this.loading2 = true
      // 切换城市清空表单信息
      this.form.data1 = ''
      this.form.data2 = ''
      this.terminalNumber = ''
      this.checkList = []

      request
        .post(host + 'beepartner/franchisee/bike/findBike')
        .withCredentials()
        .set({
          'content-type': 'application/x-www-form-urlencoded'
        })
        .send({
          cityId:this.cityCodeList.join(),
          'startOnlineTime':'',
          'endOnlineTime': '',
          'bikeState': this.checkList.toString(),
          'keyName': this.terminalNumber,
          currentPage:this.currentPage3
        })
        .end((error, res) => {
          if (error) {
            this.loading2 = false
            this.emptyText = '暂无数据'
            console.log('error:', error)
          } else {
             this.loading2 = false
            var data = JSON.parse((res.text)).data
            var newData = this.tableDataDel(data)
            this.pagetotal = JSON.parse((res.text)).totalPage
            this.tableData = newData
            this.totalItems = Number(JSON.parse((JSON.parse(res.text)).totalItems))
            // loading 关闭
           
            if (this.pagetotal > 1) {
              this.pageShow = true
            } else {
              this.emptyText = '暂无数据'
              return
            }
          }
        })
    }
  },
  watch: {
    'cityCodeList':{
      handler:function(n,o){
        this.mountedWay()
        this.currentPage3 = 1
       
      },
      deep:true
    },
    'checkList': 'handleList',
    currentPage3: {
      handler: function (val, oldVal) {
        this.loading2  = true
        var startTime = null
        var endTime = null
        if (this.form.data1 === '' || this.form.data2 === '') {
          startTime = null
          endTime = null
        } else {
          startTime = moment(this.form.data1).format('YYYY-MM-DD')
          endTime = moment(this.form.data2).format('YYYY-MM-DD')
        }
        if (this.isQuery === true) {
          var radio = this.checkList.toString()
          //return
          request
            .post(host + 'beepartner/franchisee/bike/findBike')
            .withCredentials()
            .set({
              'content-type': 'application/x-www-form-urlencoded'
            })
            .send({
              cityId:this.cityCodeList.join(),
              'startOnlineTime': startTime,
              'endOnlineTime': endTime,
              'bikeState': radio,
              'keyName': this.isSearch === false?'':this.terminalNumber,
              'currentPage': this.currentPage3
            })
            .end((error, res) => {
              if (error) {
                console.log('error:', error)
                this.loading2 = false
              } else {
                console.log(JSON.parse(res.text).data)
                var data = (JSON.parse(res.text)).data
                var newData = this.tableDataDel(data)
                // loading 关闭
                this.loading2 = false
                this.tableData = newData
              }
            })
        } else {
          request
            .post(host + 'beepartner/franchisee/bike/findBike')
            .withCredentials()
            .set({
              'content-type': 'application/x-www-form-urlencoded'
            })
            .send({
              cityId:this.cityCodeList.join(),
              'startOnlineTime': startTime,
              'endOnlineTime': endTime,
              'bikeState': radio,
              'keyName': this.isSearch === false?'':this.terminalNumber,
              'currentPage': this.currentPage3
            })
            .end((error, res) => {
              if (error) {
                console.log('error:', error)
                this.loading2 = false
              } else {
                this.loading2 = false
                var data = (JSON.parse(res.text)).data
                var newData = this.tableDataDel(data)
                this.tableData = newData
              }
            })
        }
      },
      deep: true
    },
    // "form.data1": {
    //   handler: function (val, oldVal) {
    //     if (val.toString().length === 0 && this.form.data2.toString().length === 0 && this.terminalNumber.length === 0 ) {
    //       this.mountedWay()
    //     }
    //     var startTime = new Date(val).getTime()
    //     var endTime = new Date(this.form.data2).getTime()
    //     endTime = isNaN(endTime) ? 0 : endTime
    //     console.log(endTime.toString().length)
    //     if ((startTime > endTime) && endTime.toString().length > 1) {
    //       this.$message({
    //         type: 'warning',
    //         message: '开始日期不能大于结束日期'
    //       })
    //     } else if ((startTime > endTime) && endTime.toString().length === 1) {
    //       // this.$message({
    //       //   type: 'error',
    //       //   message: '请输入结束日期'
    //       // })
    //     } else {
    //       return
    //     }
    //   },
    //   deep: true
    // },
    // "form.data2": {
    //   handler: function (val, oldVal) {
    //     if (val.toString().length === 0 && this.form.data1.toString().length === 0 && this.terminalNumber.length === 0) {
    //       this.mountedWay()
    //     }
    //     var endTime = new Date(val).getTime()
    //     var startTime = new Date(this.form.data1).getTime()
    //     startTime = isNaN(startTime) ? 0 : startTime
    //     console.log(startTime.toString().length)
    //     if ((endTime < startTime) && startTime.toString().length > 1) {
    //       this.$message({
    //         type: 'warning',
    //         message: '开始日期不能大于结束日期'
    //       })
    //     } else if ((endTime > startTime) && startTime.toString().length === 1) {
    //       this.$message({
    //         type: 'warning',
    //         message: '开始日期不能为空'
    //       })
    //     } else {
    //       return
    //     }
    //   },
    //   deep: true
    // },
    // 'terminalNumber':{
    //   handler:function(n,o){
    //     if(n.length==0){
    //        this.mountedWay()
    //     }
    //   },
    //   deep:true
    // }
  }
}
</script>
<style>
.carManager_content {
  background: #faebd7;
  padding: 20px 20px 10px 20px;
  margin-bottom: 20px;
  border: 1px solid #e7ecf1;
  color: #555;
}


/*div.carManager div.queryCarInfo {
  background: #f3f0f0;
  padding: 10px 10px 0 10px;
}*/

div.carManager div.queryCarInfo div.el-form-item {
  margin-bottom: 10px;
}

div.carManager div.queryCarInfo div.el-form-item span.labelAlign {
  float: left;
  width: 68px;
  display: block;
  text-align: right;
  margin-left:-10px;
  margin-right: 10px;
  font-size: 14px;
  margin-top:2px;
  /* color: #555; */
}

span.division {
  font-size: 14px;
  color: #555;
  width: 32px;
  display: inline-block;
  text-align: center;
}

div.filtercar {
  display: inline-block;
}

div.line {
  margin-left: 0px;
}

div.el-input {
  width: 200px;
}

div.showCarInfo {
  padding: 20px;
  background: #fff;
  border: 1px solid #e7ecf1;
  border-bottom: none;
}

div#carManager_page {
  padding: 4px 10px 0 22px;
  /* padding-bottom: 100px; */
  background: #fff;
  border: 1px solid #e7ecf1;
  border-top: none;
  min-height: 304px;
}

.carMan_bar {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  max-width:calc(100% - 83px);
  width: 428px;
  background-color: #fff;
  background-image: none;
  border-radius: 4px;
  border: 1px solid #ddd;
  box-sizing: border-box;
  color: #1f2d3d;
  font-size: inherit;
  height: 36px;
  line-height: 1;
  outline: 0;
  padding: 3px 10px;
  transition: border-color .2s cubic-bezier(.645, .045, .355, 1);
}

.my_btn {
  width: 80px;
  float: right;
  height: 36px;
  line-height: 11px;
  color: #fff;
  /*margin-top: 10px;*/
  outline: none;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background: rgba(52, 52, 67, 0.8);
}

.my_btn:hover {
  background: rgba(52, 52, 67, 0.9);
  color: #fff !important;
}

.el-input__inner {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  background-color: #fff;
  background-image: none;
  border-radius: 4px;
  border: 1px solid #ddd;
  box-sizing: border-box;
  color: #1f2d3d;
  font-size: inherit;
  height: 36px;
  line-height: 1;
  outline: 0;
  padding: 3px 10px;
  transition: border-color .2s cubic-bezier(.645, .045, .355, 1);
}

.el-date-table td.current:not(.disabled),
.el-date-table td.end-date,
.el-date-table td.start-date {
  background: black !important;
  color: #fff !important;
}

.el-input__inner:hover {
  border: 1px solid #bbb;
}

.el-button:focus,
.el-button:hover {
  color: #fff;
}

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

.el-checkbox__inner {
  border-color: #ddd !important;
}

.el-checkbox__inner:hover {
  border-color: #ddd !important;
}

.el-checkbox__input.is-checked .el-checkbox__inner {
  background-color: #444;
  border-color: #888;
  color: #fff;
}

div.carManager .el-form-item__content .el-input input.el-input__inner {
  width: 194px;
}

.el-pagination {
  margin-left: 0;
  padding-left: 0;
  margin-top: 20px;
  margin-bottom: 10px;
  border-left: 0;
}
</style>
