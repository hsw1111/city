<template>
  <div>
    <div id="container" v-bind:class={isShow:active} v-loading="loading"></div>
    <div v-show="active" class="nodata">暂无数据</div>
  </div>
</template>
<script>
var Highcharts = require('highcharts')
// 在 Highcharts 加载之后加载功能模块
require('highcharts/modules/exporting')(Highcharts)
import request from 'superagent'
import { host } from '../config/index'
export default {
  data() {
    return {
      categories: [],
      data: [],
      loading: true,
      active:true
    }
  },
  props:['cityCode'],
  methods: {
    generateAxis() {
      var nowTime = new Date().getHours().toString()
      var count = 0
     
      for (var i = nowTime; i > 0; i--) {
        count++
        if (count <= 7) {
          this.categories.unshift(Number(i) + ":00")
        } else {
          break
        }
      }
    },
    generateCharts(categories, data) {
      Highcharts.chart('container', {
        /** Highcharts 配置 */
        colors: ['#058DC7', '#50B432', '#ED561B', '#DDDF00',
                '#24CBE5', '#64E572', '#FF9655', '#FFF263', '#6AF9C4'],
        lang: {
          printChart: '打印图表',
          contextButtonTitle: '图表导出菜单',
          decimalPoint: '.',
          downloadJPEG: '下载JPEG图片',
          downloadPDF: '下载PDF文件',
          downloadPNG: '下载PNG文件',
          downloadSVG: '下载SVG文件'
        },
        plotOptions:{
          column: {
            //pointWidth: 30,
            maxPointWidth: 100 // 设置最大宽度
          }
        }, 
        credits: {
          enabled: false // 禁用版权信息
        },
        exporting: {
          enabled: false //用来设置是否显示‘打印’,'导出'等功能按钮，不设置时默认为显示  
        },
        chart: {
          type: 'column'                           // 指定图表的类型，默认是折线图（line）
        },
        title: {
          text: ' '                 // 指定图表标题
        },
        xAxis: {
          categories: categories,
          crosshair: true
        },
        yAxis: {
           allowDecimals: false,
          title: {
            text: ''
          },
          labels: {
            step: 1
          },
          plotLines: [{
            value: 0,
            width: 1,
            color: '#808080'
          }]
        },
        legend: {
          layout: 'vertical',
          align: 'left',
          verticalAlign: 'top',
          borderWidth: 0
        },
        series: [
          {
            name: ' ',
            color: '#74f7af',
            data: data,
            colorByPoint:true,
            tooltip: {
              valueSuffix: '单',
              useHTML: true,
              headerFormat: '<span style="font-size: 12px">时间 {point.key}</span><br/>',
              pointFormatter: function() {
                return '<span style="color:{' + this.series.color + '}"></span>单数: <b>' + this.y + '</b>'
              }
            }
          }
        ],
        plotOption : { 
          column : { 
              // 设置每个柱自身的宽度 
              // pointWidth : 
              // x轴每个点只用一个柱，则这个属性设置的是相邻的两个点的柱之间的间距。 
              // 如果x轴每个点有2个柱，则这个属性设置的是左侧点的右柱与右侧点的左柱之间的间距。 
              // 0.5的含义是，如果x轴长100px,则间距是100*0.5=50px 
              pointPadding : 0.1 ,
              // 如果x轴一个点有两个柱，则这个属性设置的是这两个柱的间距。 
              groupPadding : 0.1 
          } 
      } 


      })
    },
    randomColor(){
      var rgb = '255,255,255'
      var r = Math.floor(Math.random()*255)+1
      var g = Math.floor(Math.random()*255)+1
      var b = Math.floor(Math.random()*255)+1
      var color = 'rgba('+r+','+g+','+b+',0.7)'
      return color
    },
    loadData() {
     
      var that = this
      request.post(host + 'beepartner/franchisee/statistics/franchiseeTrend')
        .withCredentials()
        .set({
          'content-type': 'application/x-www-form-urlencoded'
        })
        .send({
          cityCode:this.cityCode.join()
        })
        .end((err, res) => {
          if (err) {
            console.log('err2:' + err)
            this.loading = false
          } else {
            this.loading = false
            var code = JSON.parse(res.text).resultCode
            if (code != -1) {
              var arr = JSON.parse(res.text).data
              if(arr.length>0){
                  this.active = false
              }else{
                this.active = true
              }
              
              var data = arr.reverse().splice(0,7).reverse()
              var _time = [];
                var res = data.map((item) => {
                 _time.push(item.statisticId.split('').reverse().splice(0,2).reverse().join('') + ':00')
                  // return {color:that.randomColor(), y: item.totalBill }
                  return {y: item.totalBill }
                })
                this.data = res
               
            }else{
              this.data = []
              this.active = true
            }
           
            this.generateCharts(_time,this.data)
          }
        })
    }
  },
  mounted: function() {
    var that = this
    this.generateAxis()
    //this.loadData()
   // setInterval(this.loadData, 10 * 60 * 1000)
    // 创建图表
  },
  watch:{
    'cityCode':{
      handler:function(){
        this.loadData()
      },
      deeper:true
    }
  }
}
</script>
<style>
div#container {
  width: 100%;
  height: 237px;
  padding-top: 6px;
}

div#container g.highcharts-legend-item {
  display: none;
}
div.nodata{line-height:200px;text-align:center;}
div.isShow{display: none;}
</style>
