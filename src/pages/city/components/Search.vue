<template>
  <div>
    <div class="search">
      <input
        type="text"
        class="search-input"
        placeholder="输入城市名或拼音"
        v-model='keyword'
      />
    </div>
    <div
      class="search-content"
      ref='search'
      v-show='keyword'
    >
      <ul>
        <li
          v-for='item of list'
          :key='item.id'
          class='search-item border-bottom'
          @click='handleCityClick(item.name)'
        >{{item.name}}</li>
        <li class='search-item border-bottom' v-show='hasNotData'>
          没有找到匹配数据
        </li>
      </ul>
    </div>
  </div>

</template>

<script>
import BScroll from '@better-scroll/core'
export default {
  name: 'CitySearch',
  data () {
    return {
      keyword: '',
      list: [],
      timer: null
    }
  },
  props: {
    cities: Object
  },
  computed: {
    hasNotData () {
      return !this.list.length
    }
  },
  methods: {
    handleCityClick (city) {
      this.$store.commit('changeCity', city)
      this.$router.push('/')
    }
  },
  watch: {
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      if (!this.keyword) {
        this.list = []
        return
      }
      this.timer = setTimeout(() => {
        const result = []
        for (let i in this.cities) {
          this.cities[i].forEach((value) => {
            if (value.name.indexOf(this.keyword) > -1 || value.spell.indexOf(this.keyword) > -1) {
              result.push(value)
            }
          })
        }
        this.list = result
      }, 100)
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.search, {click: true})
    })
  }
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
  .search-content
    background-color: #eee
    width: 100%
    position: absolute
    top: 1.58rem
    left: 0
    right: 0
    bottom: 0
    overflow: hidden
    z-index: 1
    .search-item
      line-height: .62rem
      padding-left: .2rem
      color: #666
      background-color: #FFF
</style>
