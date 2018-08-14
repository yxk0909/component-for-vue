<template>
  <div class="wrapper">
    <div class="header">
      <div class="cancel" @click="handlerCancel">取消</div>
      <div class="title">请选择日期</div>
      <div class="confirm" @click="handlerConfirm">确定</div>
    </div>
    <div class="picker">
      <div class="top"></div>
      <div class="bottom"></div>
      <ul class="list">
        <li class="item" ref="year">
          <div class="container">
            <div class="child" v-for="year in yearList">{{year}}</div>
          </div>
        </li>
        <li class="item" ref="month">
          <div class="container">
            <div class="child" v-for="month in monthList">{{month}}</div>
          </div>
        </li>
        <li class="item" ref="day">
          <div class="container">
            <div class="child" v-for="day in dayList">{{day}}</div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  /**
   * datePicker
   * @desc 最大只能选中当天的日期 最小为当前日期的前 100 年的 1 月 1 号
   */
  import BScroll from 'better-scroll'

  export default {
    name: "datePicker",
    data() {
      return {
        yearList: [],

        selectedYear: '',
        selectedMonth: '',
        selectedDay: '',
      }
    },
    created() {
      let date = new Date();
      this.selectedYear = date.getFullYear();
      this.selectedMonth = date.getMonth() + 1;
      this.selectedDay = date.getDate();
      this._initData();
      this.$nextTick(() => {
        this._initScroll();
        this.$nextTick(() => {
          // 初始化设置默认选中值
          let yearIndex = this.yearList.findIndex((item) => {
            return item === this.selectedYear;
          });
          if (yearIndex !== -1) {
            this.yearScroll.wheelTo(yearIndex);
          }

          let monthIndex = this.monthList.findIndex((item) => {
            return item === this.selectedMonth;
          });
          if (monthIndex !== -1) {
            this.monthScroll.wheelTo(monthIndex);
          }

          let dayIndex = this.dayList.findIndex((item) => {
            return item === this.selectedDay;
          });
          if (dayIndex !== -1) {
            this.dayScroll.wheelTo(dayIndex);
          }
        })
      });
    },
    computed: {
      // 初始化月份
      monthList() {
        let arr = [];
        let date = new Date();
        // start 开始月份   end 结束月份
        let start = 1, end = 1;
        if (this.selectedYear === date.getFullYear())
          end = date.getMonth() + 1;
        else
          end = 12;
        do {
          arr.push(start);
          start++;
        } while (start <= end);
        return arr
      },
      // 初始化天数
      dayList() {
        let arr = [];
        let list = [1, 3, 5, 7, 8, 10, 12];
        let date = new Date();
        // start 开始天数   end 结束天数
        let start = 1, end = 1;
        let index = list.findIndex((item) => {
          return this.selectedMonth === item;
        });
        if (this.selectedYear === date.getFullYear() && this.selectedMonth === (date.getMonth() + 1))
          end = date.getDate();
        else if (index !== -1)
          end = 31;
        else if (this.selectedMonth !== 2)
          end = 30;
        else if ((this.selectedYear % 4 === 0 && this.selectedYear % 100 !== 0) || this.selectedYear % 400 === 0)
          end = 29;
        else
          end = 28;

        do {
          arr.push(start);
          start++;
        } while (start <= end);
        return arr;
      }
    },
    methods: {
      _initData() {
        // 初始化年份数组
        let date = new Date();
        let yearList = [];
        let year = date.getFullYear();
        let i = year - 100;
        do {
          yearList.push(i);
          i++;
        } while (i <= year);
        this.yearList = yearList;
      },
      _initScroll() {
        if (!this.yearScroll) {
          this.yearScroll = new BScroll(this.$refs.year, {
            probeType: 3,
            wheel: {
              selectedIndex: 0,
              rotate: 0,
              adjustTime: 200
            }
          });
          this.yearScroll.on('scrollEnd', () => {
            // console.log(this.yearList[this.scroll.selectedIndex])
            // console.log(this.scroll.items[this.scroll.selectedIndex].innerHTML);
            this.selectedYear = this.yearList[this.yearScroll.selectedIndex];
          })
        } else {
          this.yearScroll.refresh();
        }

        if (!this.monthScroll) {
          this.monthScroll = new BScroll(this.$refs.month, {
            probeType: 3,
            wheel: {
              selectedIndex: 0,
              rotate: 0,
              adjustTime: 200
            }
          });
          this.monthScroll.on('scrollEnd', () => {
            // console.log(this.monthScroll);
            // console.log(this.monthScroll.items[this.monthScroll.selectedIndex].innerHTML);
            this.selectedMonth = this.monthList[this.monthScroll.selectedIndex]
          })
        } else {
          this.monthScroll.refresh();
        }

        if (!this.dayScroll) {
          this.dayScroll = new BScroll(this.$refs.day, {
            probeType: 3,
            wheel: {
              selectedIndex: 0,
              rotate: 0,
              adjustTime: 200
            }
          });
          this.dayScroll.on('scrollEnd', () => {
            this.selectedDay = this.dayList[this.dayScroll.selectedIndex]
          })
        } else {
          this.dayScroll.refresh();
        }
      },

      /**
       * 确定
       */
      handlerConfirm() {
        // 2018-8-14
        alert(this.selectedYear + '-' + this.selectedMonth + '-' + this.selectedDay)
      },
      /**
       * 取消
       */
      handlerCancel() {}
    }
  }
</script>

<style scoped>
  .wrapper {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: rgba(0, 0, 0, 0.2);
  }

  .header {
    position: absolute;
    left: 0;
    bottom: 6rem;
    width: 100%;
    height: 1rem;
    display: flex;
    align-items: center;
    background: #fff;
    padding: 0 .3rem;
    box-sizing: border-box;
  }

  .header .cancel, .header .confirm {
    font-size: .32rem;
    color: #999;
  }

  .header .confirm {
    color: #62b900;
  }

  .header .title {
    flex: 1;
    text-align: center;
    font-size: .38rem;
  }

  .picker {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 6rem;
    border-top: 1px solid #ccc;
    background: #fff;
  }

  .picker .top {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
    pointer-events: none;   /* 重要：否则不能拖动未选中部分不能滚动 */
    width: 100%;
    height: 2.6rem;
    background: linear-gradient(to top, rgba(255, 255, 255, 0.4), rgba(255, 255, 255, 0.8));
  }

  .picker .top::after {
    position: absolute;
    bottom: 0;
    left: 0;
    content: '';
    width: 100%;
    height: 1px;
    background: #333;
    display: block;
  }

  .picker .bottom {
    position: absolute;
    left: 0;
    bottom: 0;
    z-index: 1;
    pointer-events: none;   /* 重要：否则不能拖动未选中部分不能滚动 */
    width: 100%;
    height: 2.6rem;
    background: linear-gradient(to bottom, rgba(255, 255, 255, 0.4), rgba(255, 255, 255, 0.8));
  }

  .picker .bottom::after {
    position: absolute;
    top: 0;
    left: 0;
    content: '';
    width: 100%;
    height: 1px;
    background: #333;
    display: block;
  }

  .list {
    width: 100%;
    height: 100%;
    display: flex;
    box-sizing: border-box;
    padding: .2rem 0;
  }

  .list .item {
    flex: 1;
    text-align: center;
    overflow: hidden;
  }

  .list .item .container {
    width: 100%;
    margin-top: 2.4rem;
  }

  .child {
    width: 100%;
    height: .8rem;
    font-size: .32rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
