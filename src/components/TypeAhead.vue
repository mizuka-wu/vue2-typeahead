<template>
    <div class="input-group" :class="[classes]">
      <input type="text" class="form-control"
             :placeholder="placeholder"
             autocomplete="off"
             v-model="query"
             @keydown.down="down"
             @keydown.up="up"
             @keydown.enter.prevent="hit"
             @keydown.esc="reset"
             @input="update($event)"/>

      <ul v-show="hasItems" class="dropdown-menu-list dropdown-menu" role="menu" aria-labelledby="dropdownMenu">
        <li v-for="(item , index) in items" :class="{active:activeClass(index)}"
            @mousedown="hit" @mousemove="setActive(index)">
          <a v-html="highlighting(item, vue)"></a>
        </li>
      </ul>
      <ul v-if="showSearchingFlag" v-show="!hasItems&&!isEmpty" class="dropdown-menu" role="menu"
          aria-labelledby="dropdownMenu">
        <li class="active" @mousemove="setActive(index)" v-if="!loading">
          <a>
            <span v-html="NoResultText"></span>
          </a>
        </li>
        <li class="active" @mousemove="setActive(index)" v-else>
          <a>
            <span v-html="SearchingText"></span>
          </a>
        </li>
      </ul>
    </div>
</template>
<style scoped>
  .dropdown-menu-list {
    display: list-item;
    width: 100%;
  }
</style>
<script lang="babel">
  import axios from 'axios'
  export default{
    name: 'TypeAhead',
    props: {
      selectFirst: {
        // 是否选择第一个选项
        require: false,
        type: Boolean,
        default: false
      },
      queryParamName: {
        // 被替换的单词
        require: false,
        type: String,
        default: ':keyword'
      },
      limit: {
        // 最大显示量
        require: false,
        type: Number,
        default: 9999
      },
      minChars: {
        // 最小进行查询的字符数量
        require: false,
        type: Number,
        default: 2
      },
      src: {
        // 请求地址
        require: true,
        type: String
      },
      delayTime: {
        // 发送延迟时间
        require: false,
        default: 500,
        type: Number
      },
      placeholder: {
        // 是否有placeholder
        require: false,
        type: String
      },
      showSearchingFlag: {
        // 是否显示搜索状态
        require: false,
        default: false,
        type: Boolean
      },
      NoResultText: {
        // 如果显示搜索状态，无结果的文本
        require: false,
        default: 'No result',
        type: String
      },
      SearchingText: {
        // 如果显示搜索状态，搜索的文本
        require: false,
        default: 'Searching...',
        type: String
      },
      classes: {
        // 所给填写框增加的类
        require: false,
        type: String
      },
      value: {
        require: true,
        type: String
      },
      onHit: {
        require: false,
        type: Function,
        default: function (item) {
          this.query = item
        }
      },

      highlighting: {
        // 高亮结果
        require: false,
        type: Function,
        default: function (item) {
          return item.replace(this.query, `<b>${this.query}</b>`)
        }
      },

      render: {
        // 对结果进行处理
        require: false,
        type: Function,
        default: function (items) {
          return items
        }
      },

      getResponse: {
        // 如何处理得到的请求
        require: true,
        type: Function
      },

      fetch: {
        // 如何获取数据
        require: false,
        type: Function,
        default: function (url) {
          return axios.get(url)
        }
      }
    },
    data () {
      return {
        items: [],
        query: '',
        current: -1,
        loading: false,
        lastTime: 0,
        data: []
      }
    },
    methods: {
      update (event) {
        this.lastTime = event.timeStamp
        if (!this.query) {
          return this.reset()
        }

        if (this.minChars && this.query.length < this.minChars) {
          return
        }
        // 添加的延时
        setTimeout(() => {
          if (this.lastTime - event.timeStamp === 0) {
            this.loading = true

            const re = new RegExp(this.queryParamName, 'g');

            this.fetch(this.src.replace(re, this.query)).then((response) => {
              if (this.query) {
                let data = this.getResponse(response)
                this.data = this.limit ? data.slice(0, this.limit) : data
                this.items = this.render(this.limit ? data.slice(0, this.limit) : data, this)

                this.current = -1
                this.loading = false

                if (this.selectFirst) {
                  this.down()
                }
              }
            })
          }
        }, this.delayTime)
      },

      setActive (index) {
        this.current = index
      },

      activeClass (index) {
        return this.current === index
      },

      hit () {
        if (this.current !== -1) {
          this.onHit(this.items[this.current], this, this.current)
        }
        this.reset()
      },

      up () {
        if (this.current > 0) {
          this.current--
        } else if (this.current === -1) {
          this.current = this.items.length - 1
        } else {
          this.current = -1
        }
        if (!this.selectFirst && this.current !== -1) {
          this.onHit(this.items[this.current], this)
        }
      },

      down () {
        if (this.current < this.items.length - 1) {
          this.current++
        } else {
          this.current = -1
        }
        if (!this.selectFirst && this.current !== -1) {
          this.onHit(this.items[this.current], this)
        }
      },

      reset: function () {
        this.items = []
        this.loading = false
      }

    },
    watch: {
      value: function (value) {
        this.query = this.query !== value ? value : this.query
      },
      query: function (value) {
        this.$emit('input', value)
      }
    },
    computed: {
      vue: function () {
        return this
      },
      hasItems () {
        return this.items.length > 0
      },

      isEmpty () {
        return this.query === ''
      }
    },

    mounted () {
      /***
       * 使得其点击之外的部分自动收起
       */
      document.addEventListener('click', (e) => {
        if (!this.$el.contains(e.target)) {
          this.reset()
        }
      })
    }
  }
</script>

