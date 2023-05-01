<template>
    <div class="flowchart">
        <el-tooltip effect="dark" content="点击添加节点，单机节点可通过拖拽创建连接线" placement="top">
            <el-button type="primary" @click="dialogVisible = true">添加节点</el-button>
        </el-tooltip>
        <el-tooltip effect="dark" placement="top">
            <div slot="content">
                选中节点或连接线，在高亮模式下可以删除节点或连接线<br/>若节点删除，则其连接线也会删除
            </div>
            <el-button type="warning" @click="deleteNode">删除节点或连接</el-button>
        </el-tooltip>
        <el-tooltip effect="dark" content="将模板以png图片格式输出成素材" placement="top">
            <el-button type="success" @click="downloadImage">下载模板</el-button>
        </el-tooltip>
        <el-tooltip effect="dark" content="保存模板" placement="top">
            <el-button type="info" @click="saveModel">保存模板</el-button>
        </el-tooltip>
        <div ref="container" class="flowchart-container"></div>
        <el-dialog
            title="提示"
            :visible.sync="dialogVisible"
            width="30%"
            :before-close="handleClose">
            <el-form ref="form" :model="form" label-width="80px">
                <el-form-item label="节点名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="节点类型">
                    <el-select v-model="form.type" placeholder="请选择节点类型">
                        <el-option label="开始/终止" value="terminal"></el-option>
                        <el-option label="流程" value="process"></el-option>
                        <el-option label="判断" value="decision"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="cancelAddNode">取 消</el-button>
                <el-button type="primary" @click="handleAddNode">确 定</el-button>
            </span>
        </el-dialog>
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
        nodeCfg: {
            'terminal' : {
                type: 'rect',
                style: {
                    radius: 20
                }
            },
            'process' : {
                type: 'rect',
                style: {
                    radius: 0
                }
            },
            'decision' : {
                type: 'diamond',
                style: {
                    radius: 0
                }
            },
        },
        dialogVisible: false,
        form: {
            name: '',
            type: ''
        }
      };
    },
    created() {
      //获取json数据
      this.$nextTick(() => {
        this.initG6();
      });
    },
    methods: {
      initG6() {
        const _this = this;
        this.graph = new G6.Graph({
            container: _this.$refs.container,
            width: 600,
            height: 600,
            modes: {
                default: [
                    'drag-canvas', 
                    'drag-node', 
                    {
                        type: 'create-edge',
                        trigger: 'click',
                        edgeConfig: {
                            style: {
                                stroke: '#afafaf',
                                lineWidth: 5,
                            }
                        }
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
                    // radius: 20,
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
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
      cancelAddNode(formName) {
        this.form = {
            name: '',
            type: ''
        }
        this.dialogVisible = false
      },
      handleAddNode() {
        console.log('看看', this.form.type, this.nodeCfg[this.form.type].type);
        this.graph.addItem('node', {
            type: this.nodeCfg[this.form.type].type,
            id: `node-${Math.random()}`,
            label: this.form.name,
            style: this.nodeCfg[this.form.type].style,
            x: 100,
            y: 100,
        });
        // this.form = {
        //     name: '',
        //     type: ''
        // }
        this.dialogVisible = false
      },
      deleteNode() {
        // console.log('getNode', this.graph.getNodes())
        this.graph.getNodes().forEach((node) => {
            // console.log(node.hasState('selected'));
            // console.log('remove', node._cfg);
            if(node.hasState('selected')) {
                this.graph.removeItem(node);
            }
            this.graph.clearItemStates(node);
        });
        this.graph.getEdges().forEach((edge) => {
            if(edge.hasState('selected')) {
                this.graph.removeItem(edge);
            }
            this.graph.clearItemStates(edge);
        });
      },
      downloadImage() {
        this.graph.downloadFullImage(Math.random().toString(16));
      },
      saveModel() {
        this.$confirm('确定上传模板至数据库吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '上传成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '上传失败'
          });          
        });
      }
    },
  };
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.flowchart {
  padding: 20px;
}

.flowchart-container {
  margin-top: 20px;
  border: 5px solid #ccc;
  border-radius: 5px;
  max-width: 600px;
//   height: 600px;
  overflow: auto;
}
</style>