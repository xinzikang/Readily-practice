<template>
  <div class="out-wrapper" ref="outWrapper">
    <div class="score">
      <div class="score-left">
        <p class="score-num">{{seller.score}}</p>
        <p class="score-title">综合评分</p>
        <p class="score-desc">高于周边商家{{seller.rankRate}}%</p>
      </div>
      <div class="score-rigth">
        <div class="item">
          <span>服务态度</span>
          <Stars :size="36" :score="seller.serviceScore" :style="this.css"></Stars>
        </div>
        <div class="item">
          <span>饰品美味</span>
          <Stars :size="36" :score="seller.foodScore" :style="this.css"></Stars>
        </div>
        <div class="item">
          <span>送达时间</span>
          <span class="deliveryTime">{{seller.deliveryTime}}分钟</span>
        </div>
      </div>
    </div>
    <Split></Split>
    <Ratings :btnDisc="this.btnDisc" :ratings="this.ratings" :fn="this.changeRatings" ref="isOnly"></Ratings>
    <div class="list">
      <ul>
        <li v-for="item in selectRatings">
          <div class="ratings-left">
            <div class="imgBox">
              <img :src="item.avatar" alt="头像">
            </div>
          </div>
          <div class="ratings-right"></div>
        </li>
      </ul>
    </div>
  </div>
</template>


<script type="text/ecmascript-6">
  import Split from '../../split/Split.vue';
  import BScroll from 'better-scroll';
  import Ratings from '../../ratings/Ratings.vue';
  import Stars from '../../stars/stars.vue';
  export default {
    components: {
      Split,
      Ratings,
      Stars
    },
    data() {
      return {
        css: {
          'vertical-align': 'middle'
        },
        btnDisc: {  // 按钮的文字描述
          all: '全部',
          nice: '满意',
          bad: '吐槽'
        },
        onlyContent: false,
        selectRatings: this.ratings
      };
    },
    props: {
      seller: {
        type: Object
      },
      ratings: {
          type: Array
        }
      },
    created() {
      this.beShow = true;
      console.log(this.ratings, 'body中的创建时期的rating');
      this.$nextTick(() => {
        /* 如果没有绑定过 */
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.outWrapper, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      });
    },
    methods: {
      date(T) {
        let time = new Date(T);
        let year = time.getFullYear();
        let month = time.getMonth() + 1;
        let day = time.getDate();
        let hours = time.getHours();
        let minutes = time.getMinutes();
        let seconds = time.getSeconds();

        function ToDbl(n) {
          return n < 10 ? '0' + n : '' + n;
        };
        let str = year + '-' + ToDbl(month) + '-' + ToDbl(day) + '  ' + ToDbl(hours) + ':' + ToDbl(minutes) + ':' + ToDbl(seconds);
        return str;
      },
      changeRatings(num) {
        let that = this;
        console.log(this.ratings, 333);
        this.showContent();
        if (num === 2) {
          /* 如果勾选了‘只显示有内容的’选项 */
          if (this.childOnlyContent) {
            let arr = [];
            this.ratings.forEach(function (item) {
              if (item.text) {
                arr.push(item);
              }
            });
            this.selectRatings = arr;
          } else {
            /* 没有勾选只显示内容的选项 */
            this.selectRatings = this.ratings;
          }
        } else {
          let arr = [];
          this.ratings.forEach(function (item) {
            if (item.rateType === num) {
              /* 如果勾选了指显示有内容的选项 */
              if (that.childOnlyContent) {
                if (item.text) {
                  arr.push(item);
                }
              } else {
                /* 如果没有勾只显示有内容的选项 */
                arr.push(item);
              }
            }
          });
          this.selectRatings = arr;
        }
        // 重新刷新滚动
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      // 根据子组件的参数判断是不是勾选了‘只显示内容’选项
      showContent() {
        if (this.$refs.isOnly.onlyContent) {
          this.childOnlyContent = true;
        } else {
          this.childOnlyContent = false;
        }
        return this.childOnlyContent;
      }
    }
  };
</script>


<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "../../../../static/css/mixin.styl"
  @import "../../../../static/css/base.styl"
  .out-wrapper
    position: absolute
    top: 174px
    bottom:0
    left:0
    width:100%
    .score
      display: flex
      padding: 18px 24px
      .score-left
        flex: 0 0 130px
        width: 130px
        border-1px-right(rgba(7, 17, 27, 0.1))
        .score-num
          font-size: 24px
          text-align: center
          line-height: 28px
          color: rgb(255, 153, 0)
          margin-bottom: 6px
        .score-title
          font-size: 12px
          text-align: center
          line-height: 12px
          color: rgb(7, 17, 27)
          margin-bottom: 8px
        .score-desc
          font-size: 10px
          text-align: center
          line-height: 10px
          color: rgb(147, 153, 159)
      .score-rigth
        flex: 1
        margin-left: 20px
        .item
          font-size: 12px
          margin-bottom: 8px
          line-height: 18px
          & span
            margin-right: 12px
            vertical-align: top
            color: rgb(7, 17, 27)
          & .deliveryTime
            color: rgb(147, 153, 159)
</style>
