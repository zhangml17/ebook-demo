<!-- 电子书主页面 -->
<template>
  <div class="ebook">
    <transition name="slide-down">
     <div class="title-wrapper" v-show="isTitleAndMenuShow">
        <div class="left">
          <div class="icon-back icon"></div>
        </div>
        <div class="right">
          <div class="icon-wrapper">
            <span class="icon-cart icon"></span>
          </div>

          <div class="icon-wrapper">
            <span class="icon-person icon"></span>
          </div>

          <div class="icon-wrapper">
            <span class="icon-more icon"></span>
          </div>
        </div>
      </div>
    </transition>

    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu" ></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>

    <transition name="slide-up">
      <div class="menu-wrapper" v-show="isTitleAndMenuShow">
        <div class="icon-wrapper">
          <span class="icon-menu icon"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-progress icon"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-bright icon"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-A icon"></span>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Epub from 'epubjs'
global.epbu = Epub

// const DOWNLOAD_URL = '/2018_Book_AgileProcessesInSoftwareEngine.epub'
const DOWNLOAD_URL = '/jianlai.epub'
export default {
  data () {
    return {
      isTitleAndMenuShow:false
    };
  },
  mounted() {
      this.showEpub()
  },
  methods: {
    toggleTitleAndMenu(){
      this.isTitleAndMenuShow = !this.isTitleAndMenuShow
    },
    prevPage(){
      if(this.rendition){
        this.rendition.prev()
      }
    },
    nextPage(){
      if(this.rendition){
        this.rendition.next()
      }
    },
    showEpub(){
      this.book = new Epub(DOWNLOAD_URL);
      this.rendition = this.book.renderTo('read',{
        width:window.innerWidth,
        height:window.innerHeight
      });
      this.rendition.display();
    }
  }
}

</script>
<style lang='scss' scoped>
@import 'assets/styles/global';
.ebook{
  position: relative;
  .title-wrapper{
    position: absolute;
    left:0;
    top:0;
    width:100%;
    height:px2rem(48);
    display:flex;
    z-index: 101;
    box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, .15);
    background-color: beige;

    .left{
      flex:0 0 px2rem(60);
      @include  center
    }

    .right{
      flex:1;
      display:flex;
      justify-content: flex-end;

      .icon-wrapper{
        flex:0 0 px2rem(40);
        @include  center
      }
    }

    // div隐藏的位置  y方向向上全部隐藏
    &.slide-down-enter,&.slide-down-leave-to {
      transform: translate3d(0,-100%,0)
    }
    // div 出现的位置
    &.slide-down-enter-to,&.slide-down-leave {
      transform: translate3d(0,0,0)
    }
    &.slide-down-enter-active,&.slide-down-leave-active{
      transition: all .3s linear;
    }
  }
  .read-wrapper{
    .mask{
      position: absolute;
      top:0;
      left:0;
      width:100%;
      height:100%;
      display: flex;
      z-index:100;

      .left{
        flex: 0 0 px2rem(100);
      }

      .center{
        flex:1;
      }

      .right{
        flex: 0 0 px2rem(100);
      }
    }
  }

  .menu-wrapper{
    position: absolute;
    display:flex;
    left:0;
    bottom:0;
    width:100%;
    height:px2rem(48);
    background-color: beige;
    // box-shadow: 水平方向 垂直方向  ...  阴影程度15%
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
    .icon-wrapper{
      flex:1;
      @include center
    }

    &.slide-up-enter,&.slide-up-leave-to {
      // 三维转换x,y,z
      transform: translate3d(0,100%,0)
    }

    &.slide-up-enter-to,&.slide-up-leave {
      transform:translate3d(0,0,0)
    }

    &.slide-up-enter-active,&.slide-up-leave-active {
      transition: all .3s  linear
    }
  }
}
</style>
