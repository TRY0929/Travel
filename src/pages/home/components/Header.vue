<template>
  <div>
    <div class="header">
      <div class="header-left">
        <div class='iconfont back-icon'>&#xe624;</div>
      </div>
      <div class="header-input">
        <span class='iconfont'>&#xe632;</span>
        输入城市/景点/游玩/主题
      </div>
      <router-link to='/city'>
        <div class="header-right">
          {{this.$store.state.city}}
          <span class="iconfont arrow-icon">&#xe64a;</span>
        </div>
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeHeader',
  props: {
    city: String
  }
}
</script>

<style lang='stylus' scoped>
  @import '~styles/variables.styl'
  @import '~styles/mixins.styl'
  .header
    display: flex
    line-height: $headerHeight
    background: $bgColor
    color: #fff
    .header-left
      float: left
      width: .64rem
      .back-icon
        text-align: center
        font-size: .4rem
    .header-input
      flex: 1
      background-color: #FFF
      margin-top: .12rem
      margin-left: .12rem
      line-height: .62rem
      height: .62rem
      border-radius: 0.05rem
      color: #ccc
      padding-left: .18rem
    .header-right
      float: right
      width: 1.24rem
      text-align: center
      color: #fff
      ellipsis()
      .arrow-icon
        margin-left: -0.04rem
        font-size: .2rem
</style>
