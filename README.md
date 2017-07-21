# vue2-DataPick

因公司业务需要抽分出来,以后慢慢自己完善

## Requirements

* [Vue.js](https://github.com/yyx990803/vue)  2.0.0
* ~~[Scss](https://github.com/sass/sass)~~
* [ES6](https://github.com/bevacqua/es6)

## Usage

![例子样式](https://github.com/HeavenZhangHr/vue2-DataPick/blob/master/img.png)

``` bash

<template>
    <Vue2DataPicker @node-click="getDate"></Vue2DataPicker>
</template>

<script>
import Vue2DataPicker from 'Vue2-DataPick'

export default {
  methods: {
    // 节点的点击回调，获取当前点击的时间对象yyyy-MM-dd
    getDate(date) {
      console.log(date);
    }
  },
  components: {
    Vue2DataPicker
  }
}
</script>

<style>
</style>
```

## demo

``` bash

<template>
  <div class="main">
    <div class="header">
      <!-- 向左翻页 -->
      <div class="btn-block left">
        <span class="dateBtn" @click="reduceYear()">&laquo;</span>
        <span class="dateBtn" @click="reduceMonth()">&lt;</span>
      </div>
      <!-- 向右翻页 -->
      <div class="btn-block right">
        <span class="dateBtn" @click="addMonth()">&gt;</span>
        <span class="dateBtn" @click="addYear()">&raquo;</span>
      </div>
      <div class="date-block">
        <span>
          <b class="year">{{year}}</b> 年</span>
        <span>
          <b class="month">{{month}}</b> 月</span>
      </div>
    </div>
    <div class="dateContent">
      <div class="weekContent">
        <span v-for="(item,index) of week" :key="index">{{item}}</span>
      </div>
      <div class="date-list">
        <span v-for="(item,index) of previousMonth" class="lastMonth">{{item}}</span>
        <span v-for="(item,index) of monthDay[month - 1]" :class="{active:currIndex==index}" @click="selectDay($event,index)" class="nowMonth">{{item}}</span>
        <span v-for="(item,index) of nextMonth" class="nextMonth">{{item}}</span>
      </div>
    </div>
  </div>
</template>

<script type="es6">
export default {
  data() {
    return {
      week: ['日', '一', '二', '三', '四', '五', '六'],
      year: new Date().getFullYear(),
      month: new Date().getMonth() + 1,
      day: 0,
      // 上月剩余
      previousMonth: [],
      monthDay: [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
      // 下月剩余
      nextMonth: [],
      // 当前被点击索引
      currIndex: '',
    }
  },
  created() {
    this.dayScreen();
  },
  watch: {
    year(val) {
      let reg = /^[1-9]\d*$/g;
      if (!reg.test(val)) {
        let date = new Date();
        this.year = date.getFullYear();
      }
      if (val < 0) {
        this.year = 1;
      }
      if (val > 10000) {
        this.year = 10000;
      }
      this.dayScreen();
    },
    month(val) {
      let reg = /^[1-9]\d*$/g;
      if (!reg.test(val)) {
        let date = new Date();
        this.month = date.getMonth() + 1;
      }
      if (val < 1) {
        this.month = 1;
      }
      if (val > 12) {
        this.month = 12;
      }
      this.dayScreen();
    }
  },
  methods: {
    // 显示选中的日期
    isActive(index) {
      this.currIndex = index;
    },
    // 获取当前日期
    selectDay(e, index) {
      this.isActive(index);
      let currDate = new Date(this.year, this.month - 1, e.target.innerText),
        currNodeDate = this.formatDate(currDate)
      this.$emit("node-click", currNodeDate);
    },
    reduceYear() {
      this.year--
    },
    reduceMonth() {
      this.month--
    },
    addMonth() {
      this.month++
    },
    addYear() {
      this.year++
    },
    // 日期显示
    dayScreen() {
      // 上一个月
      let firstDate = new Date(this.year, this.month - 1, 1),
        // 获取上个月的第一天是周几
        firstWeek = firstDate.getDay(),
        preMonthDay = null;

      // 如果当前是1月
      if (this.month == 1) {
        // 则上月天数为12月的
        preMonthDay = this.monthDay[11];
      } else {
        // 否则就是上个月的
        preMonthDay = this.monthDay[this.month - 1];
      }
      // 遍历上个月的天数
      for (let i = 0; i < preMonthDay; i++) {
        this.previousMonth[i] = i + 1;
      }
      // 取出最后几个天数
      if (firstWeek == 0) {
        this.previousMonth = this.previousMonth.slice(-7);
      } else {
        this.previousMonth = this.previousMonth.slice(-firstWeek);
      }
      // 下一个月
      let endDate = new Date(this.year, this.month + 1, this.monthDay[this.month + 1]),
       endWeek = endDate.getDay(),
       nextMonthDay = null;
      if (this.month == 12) {
        nextMonthDay = this.monthDay[0];
      } else {
        nextMonthDay = this.monthDay[this.month];
      }
      for (let i = 0; i < nextMonthDay; i++) {
        this.nextMonth[i] = i + 1;
      }
      if (endWeek == 6) {
        this.nextMonth = this.nextMonth.slice(0, 7);
      } else {
        this.nextMonth = this.nextMonth.slice(0, 6 - endWeek);
      }
    },
    //格式化日期
    formatDate(date) {
      let d = new Date(date),
        month = '' + (d.getMonth() + 1),
        day = '' + d.getDate(),
        year = d.getFullYear();
      if (month.length < 2) month = `0${month}`;
      if (day.length < 2) day = `0${day}`;
      return [year, month, day].join('-');
    }
  },

  computed: {
  }
}
</script>

<style lang="scss" scoped>
.main {
  background: rgba(0, 0, 0, .4);
  border-color: rgba(0, 0, 0, .2);
  width: 260px;
  padding: 2px;
  line-height: 18px;
  >.header {
    position: relative;
    border: 0;
    font-weight: 700;
    width: 100%;
    padding: 4px 0;
    color: #676767;
    border-bottom: 1px solid rgba(0, 0, 0, .6);
    >.btn-block {
      display: inline-block;
      width: auto;
      >.dateBtn {
        color: #fff;
        text-decoration: none;
        vertical-align: top;
        cursor: pointer;
        text-align: center;
        margin-top: 2px;
        padding: 6px 10px;
        width: 14px;
        height: 14px;
        &:hover {
          color: #FF9600!important;
        }
      }
    }
    >.date-block {
      text-align: center;
      padding: 0;
      >span {
        font-size: 16px;
        vertical-align: top;
        >b:hover {
          cursor: pointer;
          color: #fff!important;
          background-color: rgba(255, 255, 225, .15);
        }
      }
    }
  }
  >.dateContent {
    width: 100%;
    >.weekContent {
      width: 100%; // display: flex;
    }
    >.date-list {
      font-size: 0;
      >.nowMonth {
        &:hover {
          border-radius: 50%;
        }
        &.active,
        &:hover {
          background-color: rgba(255, 255, 255, .35);
          border-color: rgba(0, 0, 0, .1)rgba(0, 0, 0, .1)rgba(0, 0, 0, .25);
          cursor: pointer;
        }
      }
      >.lastMonth,
      >.nextMonth {
        color: #54686B;
      }
    }
    span {
      display: inline-block;
      width: 36px;
      height: 36px;
      text-align: center;
      line-height: 36px;
      font-size: 14px;
    }
  }
}

.left {
  float: left;
}

.right {
  float: right;
}
</style>

```

