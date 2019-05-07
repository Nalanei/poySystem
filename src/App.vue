<template>
  <div id="app">
    <leftNav></leftNav>
    <div class="main">
      <router-view/>
    </div>
  </div>
</template>

<script>
  import LeftNav from '@/components/common/leftNav'
  export default {
    name: 'App',
    components: {
      LeftNav
    }
  }
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  .main {
    float: left;
    width: 94%;
    background-color: #EFF2F7;
    height: 100%;
    overflow: hidden;
  }
}
</style>
