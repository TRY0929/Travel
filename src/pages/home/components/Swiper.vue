<template>
  <div class="wrapper">
    <swiper :options="swiperOptions" class='swiper-img'>
      <swiper-slide v-for='item of swiperList' :key='item.id'>
        <img :src="item.imgUrl">
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>

<script>
export default {
  name: 'HomeSwiper',
  data () {
    return {
      swiperOptions: {
        pagination: {
          el: '.swiper-pagination'
        },
        loop: true
      },
      swiperList: [{
        id: '0001',
        imgUrl: 'https://imgs.qunarzz.com/sight/source/1812/1e/131ba1bc67be3e.jpg_r_640x214_edd2705d.jpg'
      }, {
        id: '0002',
        imgUrl: 'https://imgs.qunarzz.com/sight/source/1606/35/177a89a2b3a1e5.jpg_r_640x214_ba0ef2d4.jpg'
      }, {
        id: '0003',
        imgUrl: 'https://imgs.qunarzz.com/sight/source/1811/88/8d3b492ebf0b16.jpg_r_640x214_7b3a281d.jpg'
      }, {
        id: '0004',
        imgUrl: 'https://imgs.qunarzz.com/sight/source/1811/b4/71c01c98139cf.jpg_r_640x214_30bac495.jpg'
      }, {
        id: '0005',
        imgUrl: 'https://imgs.qunarzz.com/sight/source/1505/fb/302620ed8350f8.jpg_r_640x214_25a07cb4.jpg'
      }]
    }
  }
}
</script>

<style lang='stylus' scoped>
  .wrapper >>> .swiper-pagination-bullet-active
    background: #fff !important
  .wrapper
    overflow: hidden
    width: 100%
    height: 0
    padding-bottom: 36.64%
    .swiper-img
      img
        width: 100%
</style>
