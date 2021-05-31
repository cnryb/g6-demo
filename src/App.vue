<template>
  <div id="app" :class="{ 'selected-canvas': selectedCanvas }"></div>
</template>

<script>
import G6 from "@antv/g6";

export default {
  name: "App",
  data() {
    return {
      selectedCanvas: false,
    };
  },
  mounted() {
    const data = {
      // 点集
      nodes: [
        {
          type: "rect",
          id: "node2",
          label: "node2",
        },
        {
          id: "node1",
          label: "node1",
          type: "rect",
        },
        {
          type: "rect",
          id: "node3",
          label: "node3",
        },
        {
          type: "rect",
          id: "node0",
          label: "node0",
        },
      ],
      // 边集
      edges: [
        {
          source: "node3",
          target: "node1",
        },
        {
          source: "node1", // String，必须，起始点 id
          target: "node2", // String，必须，目标点 id
          label: "edge 1",
          // style: {
          //   endArrow: {
          //     path: G6.Arrow.vee(),
          //   },
          //   cursor: "pointer",
          //   lineAppendWidth: 4,
          // },
        },
        // {
        //   source: "node3",
        //   target: "node0",
        //   label: "edge 1",
        // },
      ],
    };

    // const container = document.getElementById("app");
    // const width = container.scrollWidth;
    // const height = container.scrollHeight || 500;
    const graph = new G6.Graph({
      container: "app", // String | HTMLElement，必须，在 Step 1 中创建的容器 id 或容器本身
      width: 800, // Number，必须，图的宽度
      height: 500, // Number，必须，图的高度
      fitCenter: true,
      // layout: {
      //   type: "gForce",
      //   linkDistance:200
      // },
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
        // altSelect: [
        //   {
        //     type: "click-select",
        //     trigger: "alt",
        //   },
        //   "drag-node",
        // ],
      },
    });
    graph.data(data); // 读取 Step 2 中的数据源到图上
    graph.render(); // 渲染图

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