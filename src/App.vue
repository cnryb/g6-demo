<template>
  <div id="app"></div>
</template>

<script>
import G6 from "@antv/g6";

export default {
  name: "App",
  mounted() {
    const data = {
      // 点集
      nodes: [
        {
          id: "node1", // String，该节点存在则必须，节点的唯一标识
          label: "node1",
          type: "rect",
        },
        {
          type: "rect",
          id: "node2", // String，该节点存在则必须，节点的唯一标识
          label: "node2",
        },
        {
          type: "rect",
          id: "node3", // String，该节点存在则必须，节点的唯一标识
          label: "node3",
        },
      ],
      // 边集
      edges: [
        {
          source: "node1", // String，必须，起始点 id
          target: "node2", // String，必须，目标点 id
          label: "edge 1",
          style: {
            endArrow: {
              path: G6.Arrow.vee(),
            },
          },
        },
        {
          source: "node3", // String，必须，起始点 id
          target: "node2", // String，必须，目标点 id
          label: "edge 1",
          style: {
            endArrow: {
              path: G6.Arrow.vee(),
            },
            lineWidth: 2,
            cursor: "pointer",
          },
        },
      ],
    };
    const graph = new G6.Graph({
      container: "app", // String | HTMLElement，必须，在 Step 1 中创建的容器 id 或容器本身
      width: 800, // Number，必须，图的宽度
      height: 500, // Number，必须，图的高度
      fitCenter: true,
      modes: {
        default: ["click-select", "drag-node"],
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

    graph.on("node:mouseenter", (e) => {
      graph.setItemState(e.item, "active", true);
    });

    graph.on("node:mouseleave", (e) => {
      graph.setItemState(e.item, "active", false);
    });

    graph.on("nodeselectchange", (e) => {
      console.log(e.selectedItems, e.select);
      clearSelected(e);
    });

    graph.on("edge:mouseenter", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", true);
    });

    graph.on("edge:mouseleave", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "active", false);
    });

    graph.on("edge:click", (evt) => {
      const { item } = evt;
      graph.setItemState(item, "selected", true);
    });

    graph.on("canvas:click", (evt) => {
      console.log(evt);
      clearSelected(evt);
    });

    function clearSelected(exclude) {
      graph.getNodes().forEach((node) => {
        if (node === exclude) return;
        graph.clearItemStates(node);
      });
      graph.getEdges().forEach((edge) => {
        graph.clearItemStates(edge);
      });
    }
  },
};
</script>

