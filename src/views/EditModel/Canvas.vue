<template>
    <div class="flowchart">
        <el-button type="primary" @click="handleAddNode">Add Node</el-button>
        <!-- <el-button type="primary" @click="handleAddEdge">Add Edge</el-button> -->
        <el-button type="primary" @click="deleteNode">Delete Node</el-button>
        <el-button type="primary" @click="deleteEdge">Delete Edge</el-button>
        <div ref="container" class="flowchart-container"></div>
    </div>
</template>

<script>
import G6 from "@antv/g6";

var graph = {};
//根据节点状态颜色
export default {
    name: "FlowChart",
    data() {
      return {
        dagreData: [],
      };
    },
    created() {
      //获取json数据
      this.$nextTick(() => {
        this.initG6();
      });
    },
    methods: {
    //   init() {
    //     G6.registerEdge('relation', {
    //         draw: function draw(cfg, group) {
    //             var keyShape = group.addShape('path', {
    //                     attrs: {
    //                     path: [['M', cfg.startPoint.x, cfg.startPoint.y], ['L', cfg.endPoint.x, cfg.endPoint.y]],
    //                     stroke: '#666',
    //                     lineWidth: 2,
    //                     lineAppendWidth: 4
    //                 }
    //             });
    //             var center = keyShape.getPoint(0.5);
    //             var shapeContainer = group.addGroup();

    //             var point = G6.Util.getLabelPosition(keyShape, 0, 10, 4, true);
    //             group.addShape('text', {
    //                 attrs: {
    //                 text: cfg.sourceEntity,
    //                 x: point.x,
    //                 y: point.y,
    //                 rotate: point.rotate,
    //                 fill: '#666'
    //                 }
    //             });
    //             point = G6.Util.getLabelPosition(keyShape, 1, -15, 4, true);
    //             group.addShape('text', {
    //                 attrs: {
    //                 text: cfg.targetEntity,
    //                 x: point.x,
    //                 y: point.y,
    //                 fill: '#666',
    //                 rotate: point.rotate
    //                 }
    //             });
    //             shapeContainer.transform([['t', -center.x, -center.y], ['r', point.angle], ['t', center.x, center.y]]);
    //             shapeContainer.addShape('path', {
    //                 attrs: {
    //                 path: [['M', center.x - 40, center.y], ['L', center.x, center.y - 20], ['L', center.x + 40, center.y], ['L', center.x, center.y + 20], ['Z']],
    //                 fill: '#fff',
    //                 stroke: '#666'
    //                 }
    //             });
    //             shapeContainer.addShape('text', {
    //                 attrs: {
    //                 text: cfg.relation,
    //                 x: center.x,
    //                 y: center.y,
    //                 textAlign: 'center',
    //                 textBaseline: 'middle',
    //                 fill: '#666'
    //                 }
    //             });
    //             return keyShape;
    //         }
    //     });
    //     var graph = new G6.Graph({
    //         container: 'mountNode',
    //         width: 800,
    //         height: 500,
    //         modes: {
    //         default: ['drag-node', 'drag-canvas', 'zoom-canvas']
    //         }
    //     });
    //     var data = {
    //         nodes: [{
    //         id: 'customer',
    //         label: 'customer',
    //         x: 200,
    //         y: 200,
    //         shape: 'rect',
    //         size: [60, 40]
    //     }, {
    //         id: 'customer_id',
    //         label: 'customer_id',
    //         x: 120,
    //         y: 160,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'name',
    //         label: 'name',
    //         x: 140,
    //         y: 100,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'address',
    //         label: 'address',
    //         x: 180,
    //         y: 60,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'email',
    //         label: 'email',
    //         x: 240,
    //         y: 110,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'order',
    //         label: 'order',
    //         x: 400,
    //         y: 200,
    //         shape: 'rect',
    //         size: [60, 40]
    //     }, {
    //         id: 'order_id',
    //         label: 'order_id',
    //         x: 320,
    //         y: 130,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'order_status',
    //         label: 'order_status',
    //         x: 380,
    //         y: 80,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'total_price',
    //         label: 'total_price',
    //         x: 440,
    //         y: 150,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'employee',
    //         label: 'employee',
    //         x: 380,
    //         y: 380,
    //         shape: 'rect',
    //         size: [60, 40]
    //     }, {
    //         id: 'employee_id',
    //         label: 'employee_id',
    //         x: 320,
    //         y: 440,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }, {
    //         id: 'title',
    //         label: 'title',
    //         x: 440,
    //         y: 440,
    //         shape: 'ellipse',
    //         size: [80, 40]
    //     }],
    //         edges: [{
    //         id: 'c_id',
    //         source: 'customer',
    //         target: 'customer_id'
    //     }, {
    //         id: 'c_name',
    //         source: 'customer',
    //         target: 'name'
    //     }, {
    //         id: 'c_address',
    //         source: 'customer',
    //         target: 'address'
    //     }, {
    //         id: 'c_email',
    //         source: 'customer',
    //         target: 'email'
    //     }, {
    //         id: 'o_id',
    //         source: 'order',
    //         target: 'order_id'
    //     }, {
    //         id: 'o_price',
    //         source: 'order',
    //         target: 'total_price'
    //     }, {
    //         id: 'o_status',
    //         source: 'order',
    //         target: 'order_status'
    //     }, {
    //         id: 'c_o',
    //         source: 'customer',
    //         target: 'order',
    //         relation: 'places',
    //         sourceEntity: '1',
    //         targetEntity: 'N',
    //         shape: 'relation'
    //     }, {
    //         id: 'o_e',
    //         source: 'employee',
    //         target: 'order',
    //         relation: 'finalize',
    //         sourceEntity: '1',
    //         targetEntity: 'N',
    //         shape: 'relation'
    //     }, {
    //         id: 'e_id',
    //         source: 'employee',
    //         target: 'employee_id'
    //     }, {
    //         id: 'e_title',
    //         source: 'employee',
    //         target: 'title'
    //     }]
    //     };
    //     graph.data(data);
    //     graph.render();
    //   },
      initG6() {
        const _this = this;
        this.graph = new G6.Graph({
            container: _this.$refs.container,
            width: 800,
            height: 800,
            modes: {
                default: [
                    'drag-canvas', 
                    'drag-node', 
                    {
                        type: 'create-edge',
                        trigger: 'click',
                    }
                ],
                editNode: [
                    {
                        type: 'click',
                        action: 'edit-node',
                    }, 
                    {
                        type: 'click',
                        button: 2,
                        action: 'delete-item',
                    }
                ],
                editEdge: [
                    {
                        type: 'click',
                        action: 'edit-edge',
                    }, 
                    {
                        type: 'click',
                        button: 2,
                        action: 'delete-item',
                    }
                ],
            },
            defaultNode: {
                type: 'rect',
                size: [120, 40],
                style: {
                    fill: '#C6E5FF',
                    stroke: '#5B8FF9',
                    lineWidth: 2,
                    radius: 5,
                },
                labelCfg: {
                    style: {
                    fill: '#1890ff',
                    fontSize: 14,
                    },
                },
            },
            defaultEdge: {
                type: 'polyline',
                style: {
                    stroke: '#dedede',
                    lineAppendWidth: 3,
                    endArrow: true,
                },
            },
            layout: {
                type: 'dagre',
                rankdir: 'TB',
                nodesep: 30,
                ranksep: 50,
                controlPoints: true,
            },
        });

        // 监听节点和边的添加
        this.graph.on('node:add', (evt) => {
            const { item } = evt;
            _this.graph.setMode('editNode', { itemId: item.get('id') });
        });
        this.graph.on('node:mouseenter', (evt) => {
            const { item } = evt;
            _this.graph.setItemState(item, 'active', true);
        });
        this.graph.on('node:mouseleave', (evt) => {
            const { item } = evt;
            _this.graph.setItemState(item, 'active', false);
        });
        this.graph.on('node:click', (evt) => {
            const { item } = evt;
            _this.graph.setItemState(item, 'selected', true);
        });
        
        this.graph.on('edge:add', (evt) => {
            const { item } = evt;
            _this.graph.setMode('editEdge', { itemId: item.get('id') });
        });
        this.graph.on('edge:mouseenter', (evt) => {
            // console.log('edge enter');
            const { item } = evt;
            _this.graph.setItemState(item, 'active', true);
        });
        this.graph.on('edge:mouseleave', (evt) => {
            // console.log('edge leave');
            const { item } = evt;
            _this.graph.setItemState(item, 'active', false);
        });
        this.graph.on('edge:click', (evt) => {
            // console.log('edge click');
            const { item } = evt;
            _this.graph.setItemState(item, 'selected', true);
        });
        
        this.graph.on('canvas:click', (evt) => {
            console.log('evttt', evt);
            _this.graph.getEdges().forEach((edge) => {
                console.log('edgeee', edge);
                _this.graph.clearItemStates(edge);
            });
            _this.graph.getNodes().forEach((node) => {
                console.log('nodeee', node);
                _this.graph.clearItemStates(node);
            })
        }
);
      },
      handleAddNode() {
        this.$prompt('输入节点名称', '创建节点', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
        })
            .then((value) => {
                this.graph.addItem('node', {
                    id: `node-${Math.random()}`,
                    label: value.value,
                    x: 100,
                    y: 100,
                });
            })
            .catch(() => {});
      },
      deleteNode() {
        console.log('getNode', this.graph.getNodes())
        this.graph.getNodes().forEach((node) => {
            console.log(node.hasState('selected'));
            console.log('remove', node._cfg);
            this.graph.removeItem(node);
        });
      },
      deleteEdge() {
        this.graph.getEdges().forEach((edge) => {
            this.graph.removeItem(edge);
        });
      },
      downloadImage() {
        graph.downloadFullImage(Math.random().toString(16));
      },
    },
  };
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.flowchart {
  padding: 20px;
}

.flowchart-container {
  border: 5px solid #ccc;
  border-radius: 5px;
  width: 800px;
  height: 800px;
//   overflow: auto;
}
</style>