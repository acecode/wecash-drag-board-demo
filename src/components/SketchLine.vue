<template>
    <g>
        <path
            :d="d"
            stroke="#333"
        ></path>
        <path
            :d="arrow"
            stroke="#333"
        ></path>
        <text
            :x="labelX"
            :y="labelY"
            text-anchor="middle"
            dominant-baseline="middle"
        >{{value}}</text>
    </g>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
@Component({
  computed: {
    dx() {
      const { x0, x1 } = this;
      return x1 - x0;
    },
    dy() {
      const { y0, y1 } = this;
      return y1 - y0;
    },
    atan() {
      const { dy, dx } = this;
      return Math.atan(dy / dx);
    },
    arrow() {
      //arrow edge length
      const r = 20;
      // arrow center length
      const r1 = 15;
      //backward move length
      const r2 = 10;
      // arrow half angle
      const a = Math.PI / 12;
      const { x0, y0, x1, y1, dx, atan } = this;

      // these is for right sign add to x1,y1
      const dr = dx > 0 ? -r : r;
      const dr1 = dx > 0 ? -r1 : r1;
      const dr2 = dx > 0 ? -r2 : r2;

      // arrow top angle (move back by r1)
      const x2 = x1 + dr2 * Math.cos(atan);
      const y2 = y1 + dr2 * Math.sin(atan);

      // left angle
      const ax = x1 + dr * Math.cos(atan - a);
      const ay = y1 + dr * Math.sin(atan - a);

      // arrow back angle
      const bx = x1 + dr * Math.cos(atan + a);
      const by = y1 + dr * Math.sin(atan + a);

      //right angle
      const cx = x1 + dr1 * Math.cos(atan);
      const cy = y1 + dr1 * Math.sin(atan);

      return `M ${ax},${ay} L ${x2}, ${y2} L ${bx}, ${by} L ${cx} ${cy} z`;
    },
    d() {
      const { x0, y0, x1, y1 } = this;
      return `M ${x0},${y0} L ${x1}, ${y1} z`;
    },
    labelX() {
      const { x0, x1 } = this;
      return (x0 + x1) / 2;
    },
    labelY() {
      const { y0, y1 } = this;
      return (y0 + y1) / 2;
    }
  }
})
export default class SketchLine extends Vue {
  @Prop()
  private x0: number;
  @Prop()
  private y0: number;
  @Prop()
  private x1: number;
  @Prop()
  private y1: number;
  @Prop()
  private value: string;
}
</script>

<style scoped>
</style>
