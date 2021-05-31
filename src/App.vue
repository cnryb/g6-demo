<template>
  <div>
    <div id="app" :class="{ 'selected-canvas': selectedCanvas }"></div>
    <tree v-if="false" />
  </div>
</template>

<script>
import G6 from "@antv/g6";
import Tree from "./components/Tree.vue";
const data = [
  {
    id: 324,
    name: "这个节点的名字是真的比较长呀",
    parentId: [],
    type: "TAG",
  },
  {
    id: 326,
    name: "abced node",
    parentId: [325],
    type: "TAG",
  },
  {
    id: 325,
    name: "line",
    parentId: [324],
    type: "PATH",
  },
];
let nodeList = null;
if (data.every((item) => item.parentId.length === 0)) {
  // 散点，点之间无关联
} else {
  const head = data.find((item) => item.parentId.length === 0);
  buildNodeList(head, data);
  console.log(head);
  nodeList = head;
}

function buildNodeList(head, source) {
  source.forEach((item) => {
    if (item.parentId.some((p) => p === head.id)) {
      head.child = item;
      buildNodeList(item, source);
    }
  });
}

export default {
  components: {
    Tree,
  },
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
          id: "node1",
          label: "node1",
          type: "rect",
        },
        {
          type: "rect",
          id: "node2",
          label: "node2",
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
    if (nodeList) {
      data.nodes = [];
      data.edges = [];
      let node = nodeList;
      while (node) {
        if (node.type === "TAG") {
          data.nodes.push({
            id: node.id + "",
            label: node.name,
          });
          if (node.child && node.child.type === "PATH") {
            data.edges.push({
              source: node.id + "",
              target: node.child.child.id + "",
              label: node.child.name,
            });
            node = node.child.child;
          } else {
            node = node.child;
          }
        } else {
          console.error("数据异常");
          data.edges.push({});
          node = node.child;
        }
      }
    }
    const container = document.getElementById("app");
    const width = container.scrollWidth;
    const height = container.scrollHeight || 500;
    const graph = new G6.Graph({
      container, // String | HTMLElement，必须，在 Step 1 中创建的容器 id 或容器本身
      width, // Number，必须，图的宽度
      height, // Number，必须，图的高度
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
      defaultNode: {
        type: "rect",
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