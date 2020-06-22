<template>
  <div class="icons">
    <swiper>
      <swiper-slide v-for="(page, index) of pages" :key='index'>
        <div v-for='item of page' class="icon" :key='item.id'>
          <div class="icon-img">
            <img :src='item.imgUrl' />
          </div>
          <p>{{item.content}}</p>
        </div>
      </swiper-slide>
    </swiper>
  </div>
</template>

<script>
export default {
  name: 'HomeIcons',
  data: function () {
    return {
      iconList: [{
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1803/95/f3dd6c383aeb3b02.png',
        content: '景点门票',
        id: '0001'
      }, {
        imgUrl: 'http://mp-piao-admincp.qunarzz.com/mp_piao_admin_mp_piao_admin/admin/20194/91a810441506568937171df66f754375.png',
        content: '红石林',
        id: '0002'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '株洲必游',
        id: '0003'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0004'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0005'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0006'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0007'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0008'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0009'
      }, {
        imgUrl: 'http://img1.qunarzz.com/piao/fusion/1804/ff/fdf170ee89594b02.png',
        content: '景点门票',
        id: '0010'
      }]
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
