<template>
  <div class="search">
    <input type="text" class="search-input" placeholder="输入城市名或拼音" />
  </div>
</template>

<script>
export default {
  name: 'CitySearch'
}
</script>

<style lang='stylus' scoped>
  @import '~styles/variables.styl'
 .search
   height: .72rem
   background-color: $bgColor
   padding: 0 .1rem
   .search-input
     box-sizing: border-box
     height: .62rem
     line-height: .62rem
     width: 100%
     text-align: center
     border-radius: .06rem
     color: #ccc
     padding: 0 .1rem
</style>
