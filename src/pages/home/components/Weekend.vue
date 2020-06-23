<template>
  <div>
    <div class="title">周末去哪儿</div>
    <ul>
      <li
          v-for='item of recommendList'
          :key='item.id'
          class="item border-bottom"
      >
        <div class="item-img-wrapper">
          <img class="item-img" :src="item.imgUrl" />
        </div>
        <div class="item-info">
          <p class="item-title">{{item.title}}</p>
          <p class="item-decs">{{item.decs}}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HomeWeekend',
  data () {
    return {
      recommendList: [{
        id: '0001',
        imgUrl: 'http://img1.qunarzz.com/sight/source/1812/1e/131ba1bc67be3e.jpg_r_640x214_edd2705d.jpg',
        title: '株洲方特欢乐世界',
        decs: '近期小伙伴们收藏最！多！的景点😊'
      }, {
        id: '0002',
        imgUrl: 'http://img1.qunarzz.com/sight/source/1812/1e/131ba1bc67be3e.jpg_r_640x214_edd2705d.jpg',
        title: '株洲方特欢乐世界',
        decs: '近期小伙伴们收藏最！多！的景点😊'
      }, {
        id: '0003',
        imgUrl: 'http://img1.qunarzz.com/sight/source/1812/1e/131ba1bc67be3e.jpg_r_640x214_edd2705d.jpg',
        title: '株洲方特欢乐世界',
        decs: '近期小伙伴们收藏最！多！的景点😊'
      }]
    }
  }
}
</script>

<style lang='stylus' scoped>
  @import '~styles/mixins.styl'
  .title
    background-color: #eee
    line-height: .6rem
    text-indent: .2rem
    font-weight: 700
  .item-img-wrapper
    overflow: hidden
    height: 0
    padding-bottom: 33.9%
    .item-img
      width: 100%
  .item-info
    padding: .1rem
    .item-title
      line-height: .54rem
      font-size: .32rem
      ellipsis()
    .item-decs
      line-height: .4rem
      color: #ccc
      ellipsis()
</style>
