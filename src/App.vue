<template>
  <div id="app">
      <!-- 左侧伸缩部分 -->
      <transition name='left'>
         <div v-show='leftshow' id='leftDiv' class='layout-left' :style='{marginLeft:leftDomMargin, width:leftDomWidth}'>
          this is in leftDiv
         </div>
      </transition>
      <!-- 伸缩按钮部分 -->
      <div class='layout-area' >
          <div @click='slide'>
            <span v-if='leftshow'><</span>
            <span v-if='!leftshow'>></span>
          </div>
      </div>
      <!-- 添加拖动代理div -->
      <div id='proxyDiv' class='layout-proxy' @mousedown.prevent='dragStart' :style='{left:proxyDomLeft}'>
        
      </div>
      <!-- 中间内容部分 -->
      <div id='centerDiv' class='layout-center' :style='{width:centerDomWid}'>
        this is in centerDiv
      </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data:() => {
      return{
        leftshow:true,
        layoutX:200,
        leftDomMargin:'0px',
        leftDomWidth:'200px',
        centerDomWid:'calc(100% - 20px -200px)',
        proxyDomLeft:'200px',

      }
  },
  methods:{
    /*左侧伸缩隐藏*/
    slide(){
      this.leftshow = !this.leftshow;
      let that = this;
        if(!this.leftshow){ //隐藏左侧div
          that.proxyDomLeft = -that.layoutX + 'px';
          that.leftDomMargin = -that.layoutX + 'px';
          that.centerDomWid = 'calc(100% - 20px)';
        }else{
          setTimeout(function(){
            that.proxyDomLeft = that.layoutX + 'px';
            that.leftDomMargin = '0px';
            that.centerDomWid = 'calc(100% - 20px - '+that.layoutX+'px)';
          },30)
          
        }
    },
    /*拖拽*/
    dragStart(e){
        let that = this;//保存this到that
        e.target.style.opacity = .8;
        /*当鼠标在拖动div按下时绑定鼠标移动事件*/
        document.onmousemove = function(event){
           that.proxyDomLeft = event.clientX + 'px';
         //拖动时更改代理div的位置，跟随鼠标运动
        }
        document.onmouseup = function(event){
          that.layoutX = event.clientX; //保存鼠标抬起的位置，
          e.target.style.opacity = 0; //代理div不可见
          that.leftDomWidth = event.clientX + 'px';
          that.centerDomWid = 'calc(100% - 20px - '+event.clientX+'px)';
          //还原事件
          document.onmousemove = null;
          document.onmouseup = null;
        }
    },
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
  height:calc(100% - 10px);
  width:calc(100% - 10px);
  //position:relative;
}
html{
  height:100%;
}
body{
  height:100%;
  margin:0px;
}
/* 左侧伸缩样式 */
.layout-left{
    width:200px;
    height:100%;
    float:left;
    background-color:#f0f000;
    border:1px solid #f0f000;
    transition:1.5s;
}
/* 伸缩按钮部分 */
.layout-area{
  width:10px;
  height:100%;
  float:left;
  position:relative;
}
/* 伸缩按钮居中 */
.layout-area>div{
  border:1px solid #f0f000;
  border-radius:0px 10px 10px 0px;
  position: absolute;
  left: 0px;
  right: 0px;
  top: 0px;
  bottom: 0px;
  background-color:#f0f000;
  margin: auto 0px;
  height: 30px;
  padding-top:15px;
  cursor:pointer;
  z-index:999;
}
/* 拖拽div */
.layout-proxy{
  position:absolute;
  cursor:w-resize;
  opacity:0;
  left:200px;
  width:10px;
  height:100%;
  background-color: blue;
}
/* 过渡类 */
.left-enter,.left-leave-to{
  margin-left:-200px;
}
.left-enter-active,.left-leave-active{
  transition:1.5s;
}

/* 中间部分样式 */
.layout-center{
    float:right;
    background-color:gray;
    width:calc(100% - 230px);
    height:100%;
    transition:1.5s;
    border:1px solid gray;
}

</style>
