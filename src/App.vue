<template>
  <div class="hello" id="app">
    <h1>{{ msg }}</h1>
    <h2>Result is: {{data}}</h2>
    <TypeAhead
      v-model="data"
      :classes="classes"
      :placeholder="placeholder"
      src="/data.json?keyword=:keyword"
      :getResponse="getResponse"
      :selectFirst="selectFirst"
      :limit="parseInt(limit)"
      :queryParamName="queryParamName"
      :minChars="parseInt(minChars)"
      :delayTime="parseInt(delayTime)"
      :onHit="onHit"
      :highlighting="highlighting"
      :render="render"
      :fetch="fetch"
    ></TypeAhead>
    <button
      style="margin-top: 60px;"
      type="button"
      @click="showConfig = !showConfig"
      class="btn btn-primary"
    >Config</button>
    <div id="config-container">
      <div id="config" v-show="showConfig">
        <div class="item">
          <input type="checkbox" v-model="selectFirst" />
          :selectFirst {{selectFirst}}
        </div>
        <div class="item">
          <input type="number" v-model="limit" />
          :limit {{limit}}
        </div>
        <div class="item">
          <input v-model="queryParamName" />
          :queryParamName {{queryParamName}}
        </div>
        <div class="item">
          <input type="number" v-model="minChars" />
          :minChars {{minChars}}
        </div>
        <div class="item">
          <input type="number" v-model="delayTime" />
          :delayTime {{delayTime}}
        </div>
        <div class="item">
          <input v-model="placeholder" />
          :placeholder {{placeholder}}
        </div>
        <div class="item">
          <input v-model="classes" />
          :classes {{classes}}
        </div>
        <div class="item">
          <h2>Methods:</h2>
          <p>
            getResponse: function (response) {
            <br />&nbsp;&nbsp;&nbsp;&nbsp;return response.data.data.items
            <br />},
            <br />onHit: function (item, vue) {
            <br />&nbsp;&nbsp;&nbsp;&nbsp;vue.query = item
            <br />},
            <br />highlighting: function (item, vue) {
            <br />&nbsp;&nbsp;&nbsp;&nbsp;return item.toString().replace(vue.query, `
            <b>${vue.query}</b>`)
            <br />},
            <br />render: function (items, vue) {
            <br />&nbsp;&nbsp;&nbsp;&nbsp;// 将搜索内容作为list的第一个
            <br />&nbsp;&nbsp;&nbsp;&nbsp;let newItem = [vue.query, ...items]
            <br />&nbsp;&nbsp;&nbsp;&nbsp;return newItem
            <br />},
            <br />fetch: function (url) {
            <br />&nbsp;&nbsp;&nbsp;&nbsp;return axios.get(url)
            <br />}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TypeAhead from './components/TypeAhead.vue'
import axios from 'axios'
export default {
  components: {
    TypeAhead
  },
  name: 'App',
  data () {
    return {
      msg: 'Welcome to Vue2-typeahead',
      data: '',
      showConfig: false,
      selectFirst: false,
      limit: 9999,
      queryParamName: ':keyword',
      minChars: 2,
      delayTime: 500,
      placeholder: 'Please input something',
      classes: 'typeahead'
    }
  },
  methods: {
    getResponse: function (response) {
      return response.data.data.items
    },
    onHit: function (item, vue, index) {
      vue.query = item
    },
    highlighting: function (item, vue) {
      return item.toString().replace(vue.query, `<b>${vue.query}</b>`)
    },
    render: function (items, vue) {
      // 将搜索内容作为list的第一个
      let newItem = [vue.query, ...items]
      return newItem
    },
    fetch: function (url) {
      return axios.get(url)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
h1,
h2 {
  font-weight: normal;
}

a {
  color: #42b983;
}

#config-container {
  display: flex;
  justify-content: center;
}

#config {
  position: relative;
  display: flex;
  flex-direction: column;
  text-align: left;
}

.item {
  display: inline-block;
}
</style>
<style>
.typeahead {
  margin: 0 auto;
  width: 50%;
  height: 22px;
}
</style>
