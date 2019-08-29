<template>
  <div>
    <div class="swiper-container">
      <div class="swiper-wrapper">
        <ul class="swiper-slide" v-for="(item,i) in currentList" ref="ul">
          <li v-for="(k,j) in item" @click="setCurrent(j,k.sendDate)">
            <p>{{k.week}}</p>
            <i class="iconfont icon-jin" v-if="k.now"></i>
            <span v-else :class="{active:currentClassStatus(i,j)}">{{k.showDate}}</span>
          </li>
        </ul>
      </div>
      <div class="cateory" :style="{left:cateLeft+'px'}"><i class="iconfont icon-sanjiaoxing-xia"></i></div>
    </div>
    <div class="date">
      <span class="now">{{nowDate}}</span>
      <span @click="getNow">回到今天</span>
    </div>
  </div>
</template>
<script>
  import Swiper from 'swiper'
  import 'swiper/dist/css/swiper.min.css'

  const cathWidth = +2;
  export default {
    mounted() {
      this.init();
      window.onresize=()=>{
        this.init();
      }
    },
    watch: {
      currentIndex(val) {
        console.log(val)
        this.cateLeft = Math.abs(this.$refs.ul[1].children[val].getBoundingClientRect().left + cathWidth);
      }
    },
    data() {
      return {
        currentList: [],
        currentFirstDate: null,
        currentIndex: new Date().getDay(),
        cateLeft: 0,
        nowDate:this.setNowDate(new Date())
      }
    },
    created() {
      this.now()
    },
    methods: {
      formatDate(nowDate) {
        let year = nowDate.getFullYear();
        let month = (nowDate.getMonth() + 1);
        let date = nowDate.getDate();
        let week = ['日', '一', '二', '三', '四', '五', '六'][nowDate.getDay()];
        return {
          week,
          showDate: date,
          listDate: `${month}月${date}日 ${week}`,
          sendDate: `${year}-${month}-${date}`,
          now: nowDate.toLocaleDateString() === new Date().toLocaleDateString()
        }
      },
      addDate(date, n) {
        date.setDate(date.getDate() + n);
        return date;
      },
      setDate(date) {
        let week = date.getDay();
        date = this.addDate(date, week * -1);
        this.currentFirstDate = new Date(date);
        let list = []
        for (let i = 0; i < 7; i++) {
          list.push(this.formatDate(i === 0 ? this.addDate(date, 0) : this.addDate(date, 1)));
        }
        return list
      },
      currentClassStatus(i, j) {
        return i === 1 && j === this.currentIndex
      },
      setCurrent(j,sendDate) {
        this.currentIndex = j;
        this.nowDate=sendDate;
      },
      setNowDate(nowDate){
        let year = nowDate.getFullYear();
        let month = (nowDate.getMonth() + 1);
        let date = nowDate.getDate();
        return `${year}-${month}-${date}`;
      },
      init(){
        var mySwiper = new Swiper('.swiper-container', {
          on: {
            slidePrevTransitionStart: () => {

            },
            slidePrevTransitionEnd: () => {
              mySwiper.slideTo(1, 0, false);
              let pre = this.setDate(this.addDate(this.currentFirstDate, -21)) //11
              let now = this.setDate(this.addDate(this.currentFirstDate, 7)); //18
              let next = this.setDate(this.addDate(this.currentFirstDate, 7)) //25
              this.currentList = [pre, now, next]
              setTimeout(() => {
                this.currentIndex = 0;
                this.nowDate=this.currentList[1][0].sendDate;
              }, 20)

            },
            slideNextTransitionStart: () => {

            },
            slideNextTransitionEnd: () => {
              mySwiper.slideTo(1, 0, false);
              let pre = this.setDate(this.addDate(this.currentFirstDate, -7)) //18
              let now = this.setDate(this.addDate(this.currentFirstDate, 7)); //25
              let next = this.setDate(this.addDate(this.currentFirstDate, 7)) //32
              this.currentList = [pre, now, next]
              setTimeout(() => {
                this.currentIndex = 6;
                this.nowDate=this.currentList[1][6].sendDate;
              }, 20)

            },
          },
        })
        mySwiper.slideTo(1, 0, false);
        this.$nextTick(() => {
          this.cateLeft = Math.abs(this.$refs.ul[1].children[this.currentIndex].getBoundingClientRect().left + cathWidth)
        })
      },
      getNow(){
        this.now()
        this.nowDate=this.setNowDate(new Date())
        this.currentIndex=new Date().getDay()
      },
      now(){
        let now = this.setDate(new Date());
        let pre = this.setDate(this.addDate(this.currentFirstDate, -7))
        let next = this.setDate(this.addDate(this.currentFirstDate, 14))
        this.currentList = [pre, now, next]
      }
    },
  }
</script>
<style lang="less" scoped>
  * {
    margin: 0;
    padding: 0;
  }

  .swiper-wrapper {
    height: 68px;
  }

  .swiper-container {
    position: relative;
    overflow: hidden;

    .cateory {
      width: 20px;
      height: 16px;
      position: absolute;
      left: 0;
      bottom: -2px;
      z-index: 10;
      text-align: center;
      transition: all 0.2s ease-in;

      i {
        font-size: 12px;
        color: #ffffff;
        display: inline-block;
        vertical-align: bottom;
      }
    }
  }

  .swiper-slide {
    padding: 10px;
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    background: #4d7eff;

    li {
      color: #fff;
      font-size: 11px;
      text-align: center;
      width: 25px;
      p {
        font-size: 9px;
        opacity: 0.7;

      }

      span {
        margin-top: 5px;
        display: inline-block;
      }

      .active {
        width: 18px;
        height: 18px;
        line-height: 18px;
        text-align: center;
        background: #ffffff;
        color: #4d7eff;
        border-radius: 50%;
        font-size: 10px;
      }

      i {
        font-size: 20px;
        display: inline-block;
        margin-top: 2px;
      }
    }
  }
  .date{
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
    align-items: start;
    padding: 20px 10px;
    span{
      font-size: 18px;
      &:last-of-type{
        display: inline-block;
        padding: 5px 10px;
        color: #ffffff;
        background: #4d7eff;
        border-radius: 5px;
      }
    }
  }

</style>
