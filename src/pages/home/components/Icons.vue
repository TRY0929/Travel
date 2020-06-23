<template>
  <div class="icons">
    <swiper :options="swiperOptions">
      <swiper-slide v-for="(page, index) of pages" :key='index'>
        <div v-for='item of page' class="icon" :key='item.id'>
          <div class="icon-img">
            <img :src='item.imgUrl' />
          </div>
          <p>{{item.desc}}</p>
        </div>
      </swiper-slide>
    </swiper>
  </div>
</template>

<script>
export default {
  name: 'HomeIcons',
  props: {
    iconList: Array
  },
  data () {
    return {
      swiperOptions: {
        autoPlay: false
      }
    }
  },
  computed: {
    pages () {
      const pages = []
      this.iconList.forEach((item, index) => {
        const page = Math.floor(index / 8)
        if (!pages[page]) {
          pages[page] = []
        }
        pages[page].push(item)
      })
      return pages
    }
  }
}
</script>

<style lang='stylus' scoped>
  @import '~styles/variables.styl'
  @import '~styles/mixins.styl'
  .icons >>> .swiper-container
    width: 100%
    padding-bottom: 50%
    height: 0
    overflow: hidden
  .icons
    margin-top: .2rem
    margin-bottom: .2rem
    .icon
      overflow: hidden
      float: left
      position: relative
      width: 25%
      height: 0
      padding-bottom: 25%
      text-align: center
      .icon-img
        position: absolute
        top: .1rem
        left: 0
        right: 0
        bottom: .44rem
        overflow: hidden
        img
          height: 100%
      p
        position: absolute
        left: 0
        right: 0
        bottom: 0
        line-height: .44rem
        color: darkTextColor
        ellipsis()
</style>
