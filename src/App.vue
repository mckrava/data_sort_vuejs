<template>
  <div id="app">
    <header>
      <h3>Data sorting demo</h3>
    </header>

    <Grid></Grid>

  </div>
</template>

<script>
import Grid from './components/grid.vue'

export default {
  name: 'app',
  components: {
    Grid
  }
}

</script>

<style lang="scss">


  body {
    margin: 0;
    font-family: Helvetica Neue, Arial, sans-serif;
    font-size: 14px;
    color: #000;
  }
  #app {
    header {
      padding: 10px 50px;
      border-bottom: 1px solid rgba(0, 0, 0, 0.2);
      background-color: rgba(0, 0, 0, 0.1);
    }
  }
</style>
