<template>
  <div id="app">
    <Sketch :data="tree"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Sketch from "./components/Sketch.vue";

import tree from "./tree.js";
@Component({
  components: {
    Sketch
  },
  data() {
    return {
      tree
    };
  }
})
export default class App extends Vue {}
</script>
<style lang="stylus">
#app
  font-family 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  text-align center
  color #2c3e50
  margin-top 60px
</style>
