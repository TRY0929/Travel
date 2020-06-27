<template>
  <div>
    <div class="title">热销推荐</div>
    <ul>
      <router-link
          tag='li'
          v-for='item of recommendList'
          :key='item.id'
          class="item border-bottom"
          :to="'detail/' + item.id"
      >
        <img class="item-img" :src="item.imgUrl" />
        <div class="item-info">
          <p class="item-title">{{item.title}}</p>
          <p class="item-desc">{{item.desc}}</p>
          <button class="item-btn">查看详情</button>
        </div>
      </router-link>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HomeRecommend',
  props: {
    recommendList: Array
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
  .item
    display: flex
    overflow: hidden
    height: 1.9rem
    .item-img
      height: 1.7rem
      width: 1.7rem
      padding: .1rem
    .item-info
      flex: 1
      padding: .1rem
      min-width: 0
      .item-title
        line-height: .54rem
        font-size: .32rem
        ellipsis()
      .item-desc
        line-height: .4rem
        color: #ccc
        ellipsis()
      .item-btn
        margin-top: .16rem
        line-height: .44rem
        background-color: #ff9300
        padding: 0 .2rem
        border-radius: .06rem
        color: #fff
</style>
