<template>
  <div class="header">
    城市选择
    <router-link to='/'>
      <div class='iconfont header-icon'>&#xe624;</div>
    </router-link>
  </div>
</template>

<script>
export default {
  name: 'CityHeader'
}
</script>

<style lang='stylus' scoped>
  @import '~styles/variables.styl'
  .header
    height: $headerHeight
    line-height: $headerHeight
    text-align: center
    background-color: $bgColor
    color: #fff
    font-size: .32rem
    position: relative
    .header-icon
      font-size: .4rem
      position: absolute
      width: .64rem
      text-align: center
      top: 0
      left: 0
      color: #fff
</style>
