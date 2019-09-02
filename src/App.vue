<template>
  <div id="app">
    <Table />
  </div>
</template>

<script>
import Table from './components/Table.vue'

export default {
  name: 'app',
  components: {
    Table
  }
}
</script>

<style  lang="sass">
#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  text-align: center
  color: #2c3e50
  height: 100%
  min-height: 100vh
</style>
