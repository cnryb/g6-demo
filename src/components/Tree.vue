<template>
  <div id="tree" :class="{ 'selected-canvas': selectedCanvas }"></div>
</template>

<script>
import G6 from "@antv/g6";

export default {
  data() {
    return {
      selectedCanvas: false,
    };
  },
  mounted() {
    const data = {
      id: "root",
      label: "root",
      children: [
        {
          id: "subTree1",
          label: "subTree1",
          children: [],
        },
        {
          id: "subTree2",
          label: "subTree2",
          children: [],
        },
      ],
    };

    const container = document.getElementById("tree");
    const width = container.scrollWidth;
    const height = container.scrollHeight || 500;

    const graph = new G6.TreeGraph({
      container, // String | HTMLElement，必须，在 Step 1 中创建的容器 id 或容器本身
      width, // Number，必须，图的宽度
      height, // Number，必须，图的高度
      layout: {
        type: "compactBox",
        direction: "LR",
        getHGap: function () {
          return 80;
        },
      },
      defaultNode: {
        type: "rect",
      },
      defaultEdge: {
        style: {
          endArrow: {
            path: G6.Arrow.vee(),
          },
          lineWidth: 2,
          lineAppendWidth: 5,
          cursor: "pointer",
        },
      },
      modes: {
        default: ["drag-node"],
      },
    });
    graph.data(data); // 读取 Step 2 中的数据源到图上
    graph.render(); // 渲染图
    graph.fitCenter();

    graph.on("edge:mouseenter", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", true);
    });

    graph.on("edge:mouseleave", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", false);
    });

    graph.on("edge:click", (evt) => {
      clearSelected();
      const { item } = evt;
      graph.setItemState(item, "selected", true);
    });

    graph.on("node:mouseenter", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", true);
    });

    graph.on("node:mouseleave", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", false);
    });

    graph.on("node:click", (evt) => {
      clearSelected();
      const { item } = evt;
      graph.setItemState(item, "selected", true);
    });

    graph.on("canvas:click", (evt) => {
      // evt.target.attr({
      //   fill: "#999",
      //   stroke: "#666",
      // });
      // const { item } = evt;
      // graph.setItemState(item, "selected", true);
      console.log(evt);
      clearSelected();
      this.selectedCanvas = true;
    });
    const self = this;
    function clearSelected() {
      self.selectedCanvas = false;
      graph.getNodes().forEach((node) => {
        graph.clearItemStates(node);
      });
      graph.getEdges().forEach((edge) => {
        graph.clearItemStates(edge);
      });
    }
  },
};
</script>

<style>
.selected-canvas canvas {
  background-color: #ccf9ff;
}
</style>