<template>
  <div class="sketch">
    <h3>Sketch Board</h3>
    <p>draging: {{draging ? "true" : "false"}}</p>
    <svg width="500" height="500" class="sketch-svg"
      @mouseup="onMoveEnd"
      @mousemove="onMouseMove"
    >
      <SketchLine
        v-for="line in linkedLines"
        v-bind="line"
        :key="line.from +'-'+  line.to"
      ></SketchLine>
      <SketchNode
        v-for="node in nodes"
        v-bind="node"
        :key="node.name"
        @moveStart="onMoveStart"
        @contextMenu="showNodeContextMenu"
      ></SketchNode>
      <circle
        v-if="draging"
        :cx="dragX"
        :cy="dragY"
        r="10"
        fill="green"
        fill-opacity="0.5"
      ></circle>
      <g
        v-show="isNodeContextMenu"
      >
        <text
          :x="contextMenuX"
          :y="contextMenuY"
        >
          右键菜单
        </text>
      </g>
    </svg>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import SketchNode from "./SketchNode.vue";
import SketchLine from "./SketchLine.vue";
enum ContextMenuType {
  Null = 1,
  Node,
  Line
}
@Component({
  data() {
    return {
      dragX: 0,
      dragY: 0,
      nodes: [
        {
          name: "A",
          x: 110,
          y: 110
        },
        {
          name: "B",
          x: 60,
          y: 80
        }
      ],
      lines: [
        {
          from: "A",
          to: "B",
          value: "C100"
        }
      ]
    };
  },
  components: {
    SketchNode,
    SketchLine
  },
  computed: {
    linkedLines() {
      const { lines, nodes } = this;
      return lines.map(line => {
        const { from, to } = line;
        const fromNode = nodes.find(n => n.name === from);
        const toNode = nodes.find(n => n.name === to);
        return {
          value: line.value,
          x0: fromNode.x,
          y0: fromNode.y,
          x1: toNode.x,
          y1: toNode.y
        };
      });
    },
    isNodeContextMenu() {
      return this.contextMenuType === ContextMenuType.Node;
    }
  }
})
export default class Sketch extends Vue {
  @Prop()
  private msg!: string;

  @Prop()
  private data: Object;

  private draging: boolean = false;

  private dragNode: any;
  private startX: number;
  private startY: number;
  private dragX: number;
  private dragY: number;

  // todo use emuns
  private contextMenuType: ContextMenuType = ContextMenuType.Null;
  private contextMenuX: number = 50;
  private contextMenuY: number = 50;
  onMoveStart(event, node) {
    console.log("start", event);
    const { offsetX, offsetY, pageX, pageY } = event;
    this.draging = true;
    this.dragX = offsetX;
    this.dragY = offsetY;
    this.dragNode = node;
    this.startX = pageX;
    this.startY = pageY;
    this.contextMenuType = ContextMenuType.Null;
  }
  onMouseMove(event) {
    const { offsetX, offsetY } = event;
    this.dragX = offsetX;
    this.dragY = offsetY;
  }
  onMoveEnd(event) {
    console.log("end", event);
    const { offsetX, offsetY } = event;
    if (this.dragNode) {
      const targetNode = this.nodes.find(n => n.name === this.dragNode.name);
      if (targetNode) {
        targetNode.x = offsetX;
        targetNode.y = offsetY;
      }
      this.dragNode.moveEnd();
    }
    this.dragNode = null;
    this.draging = false;
  }
  showNodeContextMenu(event, node: SketchNode) {
    const { offsetX, offsetY } = event;
    this.dragNode = node;
    console.log(event, node);
    this.contextMenuType = ContextMenuType.Node;
    this.contextMenuX = offsetX;
    this.contextMenuY = offsetY;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
.sketch
  text-align: left
</style>
