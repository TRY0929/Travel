<template>
  <div>
    <router-link
      tag='div'
      to='/'
      class="header-abs"
      v-show='showAbs'
    >
      <span class='iconfont header-abs-icon'>&#xe624;</span>
    </router-link>
    <div
      class="header-fixed"
      v-show='!showAbs'
      :style='opacityStyle'
    >
      景点详情
      <router-link to='/'>
        <div class='iconfont header-fixed-icon'>&#xe624;</div>
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailHeader',
  data () {
    return {
      showAbs: true,
      opacityStyle: {
        opacity: 0
      }
    }
  },
  methods: {
    handleScroll () {
      const top = document.documentElement.scrollTop
      if (top > 60) {
        this.showAbs = false
        this.opacityStyle.opacity = (top / 140) > 1 ? 1 : (top / 140)
      } else {
        this.showAbs = true
      }
    }
  },
  mounted () {
    window.addEventListener('scroll', this.handleScroll)
  }
}
</script>

<style lang='stylus' scoped>
  @import '~styles/variables.styl'
  .header-abs
    position: absolute
    top: .2rem
    left: .2rem
    height: .8rem
    line-height: .8rem
    text-align: center
    width: .8rem
    border-radius: .4rem
    background-color: rgba(0, 0, 0, .8)
    .header-abs-icon
      color: #fff
      font-size: .4rem
  .header-fixed
    position: fixed
    top: 0
    left: 0
    right: 0
    height: $headerHeight
    line-height: $headerHeight
    text-align: center
    background-color: $bgColor
    color: #fff
    font-size: .32rem
    .header-fixed-icon
      font-size: .4rem
      position: absolute
      width: .64rem
      text-align: center
      top: 0
      left: 0
      color: #fff
</style>
