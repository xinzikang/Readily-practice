<template>
  <div id="app">
    <div class="warpper">
      <div class="title">To-Do-List</div>
      <div class="content">
        <div class="top">
          <input type="text" placeholder="请输入内容" v-model="text">
          <button  @click="addList">确定</button>
        </div>
        <div class="text-list" v-if="listShow.length">
          <ul>
            <li v-for="(item,index) in listShow" :class="{finshed:listShow[index].isFinshed}">
              <input type="checkbox" @click="tabFinshed(index)" :checked="listShow[index].isFinshed">
              {{item.text}}
            </li>
          </ul>
        </div>
        <div class="bottom" v-if="listAll.length">
          <label class="btn" @click="allChecked">
           全选<input type="checkbox" :checked="All">
          </label>
          <label class="btn" @click="finshedChecked">
            已完成<input type="checkbox" :checked="Finshed">
          </label>
          <label class="btn" @click="unFinshedChecked">
            未完成<input type="checkbox" :checked="Unfinshed">
          </label>
          <label class="btn">
            <input type="button" value="删除" @click="remove">
          </label>
        </div>
        <div class="no-list" v-if="!listShow.length">
          <p>暂时没有内容</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      All: false,
      Finshed: false,
      Unfinshed: true,
      listAll: [], //所有的list内容
      listShow: [],
      text:'' //input输入框中的内容
    }
  },
  methods: {
    addList() {
      if( this.text == ''){
        return;
      }
      let newList = {};
      newList.id = this.listAll.length;
      newList.text = this.text;
      newList.isFinshed = false;
      this.listAll.push(newList);
      if(this.Unfinshed) {
        this.listShow.push(newList);
      }
      if(this.All) {
        this.listShow.push(newList);
        this.All = false;
      } else{
        if(!this.All && !this.Finshed && !this.Unfinshed){ //三种类型都咩有打勾，则默认全选
          this.listShow.push(newList);
        }
      }

      this.text = '';
    },
    //每个list前面的选择框
    tabFinshed(index) {
      this.listShow[index].isFinshed = !this.listShow[index].isFinshed;
      this.All = true;
      this.listShow.forEach((item) => {
        if(!item.isFinshed){
          this.All = false;
        }
      })
      if(this.All){
        this.Finshed = false;
        this.Unfinshed = false;
      }
//      console.log(this.listShow[index].isFinshed);
    },
    allChecked() {
      this.All = !this.All;
      this.Finshed = false;
      this.Unfinshed =false;
      this.listShow = [];
      this.listAll.forEach((item) => {
        this.listShow.push(item);
      });
      if(this.All){
        this.listShow.forEach((item) => {
          item.isFinshed = true;
        })
      } else{
        this.listShow.forEach((item) => {
          item.isFinshed = false;
        })
      }
    },
    finshedChecked() {
      this.listShow = [];
      this.Finshed = !this.Finshed;
      this.Unfinshed =false;
      this.All = false;
      this.listAll.forEach((item) => {
        if(item.isFinshed) {
          this.listShow.push(item);
        }
      })
    },
    unFinshedChecked() {
      this.listShow = [];
      this.Unfinshed = !this.Unfinshed;
      this.All = false;
      this.Finshed = false;
      this.listAll.forEach((item) => {
        if(!item.isFinshed) {
          this.listShow.push(item);
        }
      })
    },
    remove() { //删除的bug，因为listAll是会动态变化的,所以可以采取for循环的方式，i--
      /*let a = [];
      this.listAll.forEach((item) => {
        a.push(item);
      });
      a.forEach((item,index) => {
        if(item.isFinshed) { //完成前面有对勾
          if(index == 0) {
            this.listAll.splice(index,1);
          } else{
            index -=1;
            this.listAll.splice(index,1);

          }

        }
      });*/
      let len = this.listAll.length;
      for(let i=0;i<this.listAll.length;i++){
        if(this.listAll[i].isFinshed){
          this.listAll.splice(i,1);
          i--;
        }
      }
      if(this.listAll.length === len) { //如果listAll的长度没哟变化，说明没有需要删除的项，直接推出
        return;
      }
      console.log(this.listAll);
      this.listShow = [];
      if(this.All){
        this.listAll.forEach((item) => {
          this.listShow.push(item);
        })
      }
      if(this.Finshed) {
        this.listAll.forEach((item) =>{
          if(item.isFinshed){
            this.listShow.push(item);
          }
        })
      }
      if(this.Unfinshed){
        this.listAll.forEach((item) => {
          if(!item.isFinshed){
            this.listShow.push(item);
          }
        })
      }
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 60px
  .warpper
    width:50%
    margin: 0 auto
    background-color: #eee
    .title
      color: red
      font: 30px/60px ''
      height:60px
    .content
      box-sizing: border-box
      padding: 2em
      width:100%
      background-color: #eecbb7
      .top
        input
          width: 100%
          height: 40px
          margin-bottom: 1em
        button
          text-align: right
          margin-bottom: 1em
      .text-list
        padding: 1em
        background-color: #fd9f7b
        color: #000
        li
          vertical-align: middle
          text-align: left
          margin: 6px 0
        .finshed
            color: #ccc
          & input
            vertical-align: middle
      .bottom
        margin: 1em 0
        .btn
          margin-right: 1em
      .no-list
        height: 40px
        color: #aaa
        font: 20px/40px ''
</style>
