<!-- 电子书主页面 -->
<template>
  <div class="ebook">
    <title-bar :isTitleAndMenuShow="isTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu" ></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :isTitleAndMenuShow="isTitleAndMenuShow"
      ref="menuBar" :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onProgressChange="onProgressChange"
      :navigation="navigation"
      @jumpTo="jumpTo"
    ></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
global.epbu = Epub

const DOWNLOAD_URL = '/2018_Book_AgileProcessesInSoftwareEngine.epub'
// const DOWNLOAD_URL = '/jianlai.epub'
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data () {
    return {
      isTitleAndMenuShow:false,
      fontSizeList:[
        {fontSize:12},
        {fontSize:14},
        {fontSize:16},
        {fontSize:18},
        {fontSize:20},
        {fontSize:22},
        {fontSize:24}
      ],
      defaultFontSize: 16,
      themeList:[
        {
          name:'default',
          style:{
            body:{
              'color':'#000',
              'background':'#fff'
            }
          }
        },
                {
          name:'eye',
          style:{
            body:{
              'color':'#000',
              'background':'#ceeaba'
            }
          }
        },
                {
          name:'night',
          style:{
            body:{
              'color':'#fff',
              'background':'#000'
            }
          }
        },
                {
          name:'gold',
          style:{
            body:{
              'color':'#000',
              'background':'rgb(241,236,226)'
            }
          }
        }
      ],
      defaultTheme: 0,
      // 图书是否处于可用状态
      bookAvailable: false,
      navigation:{}
    };
  },
  mounted() {
      this.showEpub()
  },
  methods: {
    // 根据目录章节链接跳转到指定位置
    jumpTo(href){
      this.rendition.display(href)
      // 隐藏目录和标题栏
      this.hideTitleAndMenu();
      // 隐藏目录
      this.$refs.menuBar.hideContent();
    },
    hideTitleAndMenu(){
      // 隐藏菜单栏和标题栏
      this.isTitleAndMenuShow = false;
      // 隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideSettings();
      // 隐藏弹出的目录
      // this.$refs.menuBar.hide
    },
    // progress是一个0-100的数字
    onProgressChange(progress){
      const percentage = progress / 100
      // 进度定位
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location);
    },
    setTheme(index){
      if(this.themes){
        this.themes.select(this.themeList[index].name);
        this.defaultTheme = index;
      }
    },
    registerTheme(){
      this.themeList.forEach(theme => {
        this.themes.register(theme.name,theme.style);
      });
    },
    setFontSize(fontSize){
      this.defaultFontSize = fontSize;
      if(this.themes){
        this.themes.fontSize(fontSize+'px');
      }
    },
    toggleTitleAndMenu(){
      this.isTitleAndMenuShow = !this.isTitleAndMenuShow;
      if(!this.isTitleAndMenuShow){
        // 选择menuBar这个DOM元素并调用其下的hideSettings方法
        this.$refs.menuBar.hideSettings();
      }
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
      // 获取themes对象
      this.themes = this.rendition.themes;
      // 设置默认字体
      this.setFontSize(this.defaultFontSize);
      // 注册主题
      this.registerTheme();
      // 选择默认主题
      this.setTheme(this.defaultTheme);
      // 获取Locations对象
      // 通过epubjs的钩子函数实现获取该对象
      this.book.ready.then(()=>{
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then( (result)=>{
          this.locations = this.book.locations
          this.bookAvailable = true;
      })
    }
  }
}

</script>

<style lang='scss' scoped>
@import 'assets/styles/global';
.ebook{
  position: relative;
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

}
</style>
