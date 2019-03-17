<!-- 菜单栏的组件 -->
import { deflate } from 'zlib';
<template>

  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper"
        :class="{'hide-box-shadow':isSettingShow || !isTitleAndMenuShow}"
        v-show="isTitleAndMenuShow">
        <div class="icon-wrapper">
          <span class="icon-menu icon" @click="showSettings(3)"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-progress icon" @click="showSettings(2)"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-bright icon" @click="showSettings(1)"></span>
        </div>

        <div class="icon-wrapper">
          <span class="icon-A icon" @click="showSettings(0)"></span>
        </div>
      </div>
    </transition>

    <transition name="slide-up">
      <div class="setting-wrapper" v-show="isSettingShow" >
        <div class="setting-font-size" v-if="showTag === 0">
          <div class="preview" :style="{'fontSize':fontSizeList[0].fontSize+'px'}">A</div>
          <div class="select">
            <div class="select-wrapper" v-for="(item,index) in fontSizeList" :key="index" @click="setFontSize(item.fontSize)">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize === item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview" :style="{'fontSize':fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
        </div>

        <div class="setting-theme" v-else-if="showTag === 1">
          <div class="setting-theme-item" v-for="(item,index) in themeList" :key="index"
            @click="setTheme(index)">
            <div class="preview" :style="{'background':item.style.body.background}"
             :class="{'no-border':item.style.body.background != '#fff'}"></div>
            <div class="text" :class="{'selected':index === defaultTheme}">{{ item.name }}</div>
          </div>
        </div>

        <div class="setting-progress" v-else-if="showTag ===2">
          <div class="progress-wrapper">
            <input type="range" class="progress"
                  max="100" min="0" step="1"
                  @change="onProgressChange($event.target.value)"
                  @input="onProgressInput($event.target.value)"
                  :value="progress"
                  :disabled="!bookAvailable"
                  ref="progress">
          </div>
          <div class="text-wrapper">
            <span>{{ bookAvailable ? progress+'%':'加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>

    <content-view :isShowContent="isShowContent"
                  v-show="isShowContent"
                  :navigation="navigation"
                  :bookAvailable="bookAvailable"
                  @jumpTo="jumpTo"></content-view>
    <transition name="fade">
      <div class="content-mask" v-show="isShowContent"
                                @click="hideContent"></div>
    </transition>
  </div>

</template>

<script>
import ContentView from '@/components/ContentView'
export default {
  components:{
    ContentView
  },
  props:{
    isTitleAndMenuShow:{
      type:Boolean,
      default:false
    },
    fontSizeList:{
      type:Array
    },
    defaultFontSize:Number,
    defaultTheme:Number,
    themeList:Array,
    bookAvailable:Boolean,
    navigation:Object
  },
  data () {
    return {
      isSettingShow:false,
      showTag: 0,
      progress: 0,
      isShowContent:false
    };
  },
  methods:{
    // 隐藏目录
    hideContent(){
      this.isShowContent = false
    },
    jumpTo(href){
      this.$emit('jumpTo',href)
    },
    // 拖动进度条时触发的事件
    onProgressInput(progress){
      this.progress = progress;
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    onProgressChange(progress){
      this.$emit('onProgressChange',progress)
    },
    setTheme(index){
      this.$emit('setTheme',index);
    },
    setFontSize(fontSize){
      this.$emit('setFontSize',fontSize)
    },
    showSettings(showTag){
      this.showTag = showTag;

      if(showTag == 3){
        this.isSettingShow = false;
        this.isShowContent = true;
      }else{
        this.isSettingShow = true;
      }
    },
    hideSettings(){
      this.isSettingShow = false;
    }
  }
}
</script>

<style lang='scss' scoped>
@import '../assets/styles/global';

.menu-bar{
  .menu-wrapper{
    position: absolute;
    display:flex;
    left:0;
    bottom:0;
    width:100%;
    height:px2rem(48);
    background-color: white;
    // box-shadow: 水平方向 垂直方向  ...  阴影程度15%
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
    z-index:101;
    .icon-wrapper{
      flex:1;
      @include center;
    }

    &.hide-box-shadow{
       box-shadow: none;
    }
  }

  .setting-wrapper {
    position: absolute;
    bottom:px2rem(48);
    left:0;
    width:100%;
    height:px2rem(60);
    background-color: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
    z-index:102;

    .setting-font-size {
      display: flex;
      height:100%;

      .preview{
        flex: 0 0 px2rem(40);
        @include center
      }

      .select{
        display: flex;
        flex:1;

        .select-wrapper {
          flex: 1;
          // 下面两行都是居中显示
          // @include center;
          align-items: center;
          display:flex;
          // 伪类选择器
          &:first-child {
            .line{
              &:first-child{
                border-top:none;
              }
            }
          }

          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }

          .line {
            flex:1;
            height:0;
            border-top: px2rem(1) solid #ccc;
          }

          .point-wrapper {
            position: relative;
            flex: 0 0 0;
            width:0;
            height:px2rem(7);
            border-left: px2rem(1) solid #ccc;

            .point {
              position: absolute;
              top:px2rem(-8);
              left:px2rem(-11);
              width:px2rem(20);
              height:px2rem(20);
              border-radius: 50%;
              border:px2rem(1) solid #ccc;
              background-color: white;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, .15);
              @include center;

              .small-point {
                width:px2rem(4);
                height:px2rem(4);
                border-radius: 50%;
                background-color: #000;
              }
            }
          }
        }
      }
    }

    .setting-theme {
      display: flex;
      height:100%;

      .setting-theme-item {
        flex:1;
        display: flex;
        // 列方向布局
        flex-direction: column;
        padding:px2rem(8);
        box-sizing: border-box;

        .preview {
          flex:1;
          border: px2rem(1) solid #ccc;
          box-sizing: border-box;
          &.no-border {
            border:none;
          }
        }

        .text {
          flex: 0 0 px2rem(20);
          padding-top: px2rem(5);
          font-size: px2rem(14);
          color:#ccc;
          @include center;

          &.selected {
            color:#333;
          }
        }
      }
    }

    .setting-progress {
      position: relative;
      width:100%;
      height:100%;

      .progress-wrapper {
        width:100%;
        height:100%;
        @include center;
        padding:0 px2rem(30);
        box-sizing: border-box;

        .progress {
          width:100%;
          // 覆盖默认样式
          -webkit-appearance: none;
          height:px2rem(2);
          // 设置两种背景
          background: -webkit-linear-gradient(#999,#999) no-repeat,#ddd;
          background-size: 0 100%;

          &:focus {
            outline: none;
          }
          // 进度条拖动的圆形手柄
          &::-webkit-slider-thumb {
             -webkit-appearance: none;
             height:px2rem(20);
             width:px2rem(20);
             border-radius:50%;
             background:white;
             box-shadow:0 px2rem(4) px2rem(4) pgba(0,0,0,.15);
             border:px2rem(1) solid #ccc;

          }
        }
      }

      .text-wrapper {
        position: absolute;
        left:0;
        bottom:0;
        width:100%;
        color:#333;
        font-size: px2rem(12);
        text-align: center;
      }
    }
  }

  .content-mask {
    position: absolute;
    top:0;
    left:0;
    z-index:101;
    display: flex;
    width:100%;
    height:100%;
    background:rgba(51,51,51,.8)
  }
}

</style>
