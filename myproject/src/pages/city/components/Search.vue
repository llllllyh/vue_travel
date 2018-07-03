<template>
  <div>
    <div class="search">
      <input class="search-input" v-model="keyword"  type="text"  placeholder="输入城市名或拼音" />
    </div>
    <div class="search-content" ref="search" v-show="keyword">
      <ul>
        <li class="search-item border-bottom" v-for="item of list" :key="item.id" @click="handleCityClick(item.name)">{{item.name}}</li>
        <li v-show="isNotFound" class="search-item border-bottom">没有找到匹配数据</li>
      </ul>
    </div>
  </div>
</template>

<script>
import Bscroll from 'better-scroll'
import { mapMutations } from 'vuex'
export default {
  name: 'CitySearch',
  props: {
    cities: Object
  },
  data () {
    return {
      keyword: '',
      list: [],
      timer: null,
      isNotFound: false
    }
  },
  methods: {
    handleCityClick (city) {
      this.keyword = ''
      this.changeCity(city)
      this.$router.push('/')
    },
    ...mapMutations(['changeCity'])
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
            if (value.spell.indexOf(this.keyword) > -1 || value.name.indexOf(this.keyword) > -1) {
              result.push(value)
            }
          })
        }
        this.isNotFound = result.length <= 0
        this.list = result
      }, 100)
    }
  },
  mounted () {
    this.scroll = new Bscroll(this.$refs.search, {
          mouseWheel: true, click: true, tap: true  //better-scroll 默认会阻止浏览器的原生 click 事件。当设置为 true，better-scroll 会派发一个 click 事件
        })
  }
}
</script>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .search
    height:.62rem
    padding:.1rem
    background:$bgColor
    .search-input
      width:100%
      text-align:center
      line-height:.62rem
      height:.62rem
      border-radius:.06rem
      color:#666
      padding:0 .1rem
      box-sizing:border-box
  .search-content
    z-index:1
    overflow:hidden
    position:absolute
    top:1.69rem
    left:0
    bottom:0
    right:0
    background:#eee
    .search-item
      line-height:.62rem
      padding-left:.2rem
      color:#666
      background:#fff
</style>
