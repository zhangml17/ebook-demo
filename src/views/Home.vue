<template>
  <div class="ebook">
      <div id="read" >
        <span class="icon-book"></span>
        <span class="icon-check"></span>
      </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Epub from 'epubjs'
global.epub =  Epub

export default {
  name:'home'
}
</script>
<style scoped>
  .icon-book{
    font-size:50px;
    color:red;
  }
</style>
