<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Result is: {{data}}</h2>
    <TypeAhead
      v-model="data"
      classes="typeahead"
      placeholder="Please input something"
      src="/static/data.json?keyword=:keyword"
      :getResponse="getResponse"
    ></TypeAhead>
  </div>
</template>

<script>

  import TypeAhead from './TypeAhead.vue'
  export default {
    name: 'hello',
    data () {
      return {
        msg: 'Welcome to Vue2-typeahead',
        data: ''
      }
    },
    methods: {
      getResponse: function (response) {
        return response.data.data.items
      }
    },
    components: {
      TypeAhead
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }

  a {
    color: #42b983;
  }

</style>
<style>
  .typeahead {
    margin: 0 auto;
    width: 50%;
    height: 22px;
  }
</style>
