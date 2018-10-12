<template>
  <g
    @mousedown="onMouseDown"
    @contextmenu="onContextMenu"
  >
    <circle
      r="10"
      :cx="cx"
      :cy="cy"
      :fill="draging ? 'red' : 'white'"
      stroke="black"
    ></circle>
    <text
      :x="cx"
      :y="cy"
      text-anchor="middle"
      dominant-baseline="middle"
      :fill="draging ? 'white' : 'black'"
    >
      {{name}}
    </text>
  </g>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component({
  computed: {
    cx() {
      return this.x;
    },
    cy() {
      return this.y;
    }
  }
})
export default class SketchNode extends Vue {
  @Prop()
  private msg!: string;

  @Prop({
    default: 0
  })
  private x: number;
  @Prop({
    default: 0
  })
  private y: number;

  @Prop({
    default: "node"
  })
  private name!: string;
  private draging: boolean = false;
  onMouseDown(event: any) {
    // right click
    if (event.which === 3) {
      this.onContextMenu(event);
      return;
    }
    this.draging = true;
    this.$emit("moveStart", event, this);
  }
  onContextMenu(event) {
    event.preventDefault();
    this.$emit("contextMenu", event, this);
  }
  moveEnd() {
    this.draging = false;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
.sketch-node
  position absolute
  background rgba(#888, 0.3)
</style>
