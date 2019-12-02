<template>
  <div id="selectCar">
    <div class="lis tx-l bgwhite mt10 padding-020 ov-h" id="showDateArea">
            <p class="title f14">日期选择</p>
            <div class="show-date">
              <!-- 年份 月份 -->
                <div class='month'>
                    <ul>
                        <!--点击会触发pickpre函数，重新刷新当前日期 @click(vue v-on:click缩写) -->
                        <li class='arrow' @click='pickPre(currentYear,currentMonth)' id="prevArrow">
                          <span class="f16">&lt;</span>
                        </li>
                        <li class='year-month'>
                            <span class='choose-year'>{{ currentYear }}年</span>
                            <span class='choose-month'>{{ currentMonth }}月</span>
                        </li>
                        <li class='arrow' @click='pickNext(currentYear,currentMonth)' id="nextArrow">
                          <span class="f16">&gt;</span>
                        </li>
                    </ul>
                </div>
                <!-- 星期 -->
                <ul class='weekdays'>
                    <li>周日</li>
                    <li>周一</li>
                    <li>周二</li>
                    <li>周三</li>
                    <li>周四</li>
                    <li>周五</li>
                    <li>周六</li>
                </ul>
                <!-- 日期 -->
                <ul class='days'>
                    <!-- 核心 v-for循环 每一次循环用<li>标签创建一天 -->
                    <li  v-for='(dayobject,i) in days' :key='i' @click="getDayTime(dayobject.day)">
                        <!--本月-->
                        <!--如果不是本月  改变类名加灰色-->
                        <span v-if='dayobject.day.getMonth()+1 != currentMonth' class='other-month dayNums'>{{ dayobject.day.getDate() }}</span>
                        <!--如果是本月  还需要判断是不是这一天-->
                        <span 
                        :data='dayobject.day.getFullYear()+"-"+(dayobject.day.getMonth()+1)+"-"+(dayobject.day.getDate()<10?"0"+dayobject.day.getDate():dayobject.day.getDate())' 
                        class="redFlag"
                        v-else>
                      <!--今天  同年同月同日-->
                            <span v-if='dayobject.day.getFullYear() == new Date().getFullYear() && dayobject.day.getMonth() == new Date().getMonth() && dayobject.day.getDate() == new Date().getDate()' class='active dayNums'>
                              <span>{{ dayobject.day.getDate()}}</span>
                              <div class="showFeeSpan f10 mt5 sss"></div>
                            </span>
                            <span class="dayNums" v-else>
                              <span>{{ dayobject.day.getDate() }}</span> 
                              <div class="showFeeSpan f10 mt5"></div>  
                            </span>
                        </span>
                    </li>
                </ul>
            </div>
          </div>
          
  </div>
</template>
<script>
export default {
  data() {
    return {

      // 日历选择
      currentDay: 1,
      currentMonth: 1,
      currentYear: 1970,
      currentWeek: 1,
      days: [],
      nums:0,
      dateFee:[],//存储每个月的车辆价格
    };
  },
  components: {
  
  },
  methods: {

    // 自定义日历方法
    initData: function (cur) {
      // var leftcount = 0 // 存放剩余数量
      var date
      if (cur) {
        date = new Date(cur)
      } else {
        var now = new Date()
        var d = new Date(this.formatDate(now.getFullYear(), now.getMonth(), 1))
        d.setDate(35)
        date = new Date(this.formatDate(d.getFullYear(), d.getMonth() + 1, 1))
      }
      this.currentDay = date.getDate()
      this.currentYear = date.getFullYear()
      this.currentMonth = date.getMonth() + 1
      this.currentWeek = date.getDay() // 1...6,0
      if (this.currentWeek === 0) {
        this.currentWeek = 7
      }
      var str = this.formatDate(
        this.currentYear,
        this.currentMonth,
        this.currentDay
      )
      this.days.length = 0
      // 今天是周日，放在第一行第7个位置，前面6个
      // 初始化本周
      for (var i = this.currentWeek; i >= 0; i--) {
        var d2 = new Date(str)
        d2.setDate(d2.getDate() - i)
        var dayobjectSelf = {} // 用一个对象包装Date对象  以便为以后预定功能添加属性
        dayobjectSelf.day = d2
        this.days.push(dayobjectSelf) // 将日期放入data 中的days数组 供页面渲染使用
      }
      // 其他周
      for (var j = 1; j <= 41 - this.currentWeek; j++) {
        var d3 = new Date(str)
        d3.setDate(d3.getDate() + j)
        var dayobjectOther = {}
        dayobjectOther.day = d3
        this.days.push(dayobjectOther)
      }
    },
    pickPre(year, month) {
         if(this.nums<=0){
            this.$createToast({
                txt:'最少只可查看当前月',
                type:'txt'
            }).show()
            return;
         }else{
            this.nums--;
         }
      // setDate(0); 上月最后一天
      // setDate(-1); 上月倒数第二天
      // setDate(dx) 参数dx为 上月最后一天的前后dx天
      var d = new Date(this.formatDate(year, month, 1))
      d.setDate(0)
      this.initData(this.formatDate(d.getFullYear(), d.getMonth() + 1, 1))
    },
    pickNext(year, month) {
        if(this.nums>5){
            this.$createToast({
                txt:'最多可查看6个月',
                type:'txt'
            }).show()
            return;
        }else{
            this.nums++
        }
      var d = new Date(this.formatDate(year, month, 1))
      d.setDate(35)
      this.initData(this.formatDate(d.getFullYear(), d.getMonth() + 1, 1))
    },
    // 返回 类似 2016-01-02 格式的字符串
    formatDate(year, month, day) {
      var y = year
      var m = month
      if (m < 10) m = '0' + m
      var d = day
      if (d < 10) d = '0' + d
      return y + '-' + m + '-' + d
    },
  },
  created(){
    
  },
  mounted() {
   this.initData(null)
  }
};
</script>
<style lang='less' scoped>
#selectCar {
  position: fixed;
  width: 100%;
  height: 100vh;
}
.month {
  width: 100%;
  color: #333333;
}
.month ul {
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: space-between;
  height: 35px;
}
.year-month {
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.choose-month,.choose-year{
  text-align: center;
  font-size: 16px;
}
.arrow {
  padding: 15px;
}
 
.month ul li {
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 3px;
}
.weekdays {
  margin: 0;
  padding:15px;
  padding-bottom: 0;
  display: flex;
  flex-wrap: wrap;
  font-size: 14px;
  justify-content: space-around;
}
.weekdays li {
  display: inline-block;
  width: 13.6%;
  text-align: center;
}
.days {
  padding: 10px;
  background: #ffffff;
  margin: 0;
  display: flex;
  flex-wrap: wrap;
}
.days li {
  list-style-type: none;
  display: inline-block;
  width: 14.2%;
  overflow: hidden;
  text-align: center;
  font-size: 12px;
}
.days li .dayNums{
  display: inline-block;
  width: 100%;
  height: 100%;
  padding-top:10px;
  padding-bottom: 7px;
}
.days li .active {
  background: #3089e7;
  color: #fff;
}
.days li .other-month {
  color: gainsboro;
}
.days li:hover .dayNums {
    background: #3089e7;
    color: #fff;
}
#nextArrow,#prevArrow{
  position: relative;
  span{
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
    width:12px;
    height: 30px;
    font-size: 25px;
  }
}
</style>
