<template>
<div style="box-sizing:border-box">
  <a-row type="flex" :gutter="10">
    <a-col :sm="4" :xs="24">
      <a-card :bordered="false" title="任务列" style="margin-bottom: 20px;height:100%">
        <a-row :gutter="5">
          <a-col :sm="24" :xs="24">
            <DTree v-if="treeDataLoaded"
                   :datas="treeDatas"
                   ref="tree"
                   @onAfterClickedCb="treeAfterClickedCb"></DTree>
          </a-col>
        </a-row>
      </a-card>
    </a-col>
    <a-col :sm="20" :xs="24">
      <a-card :bordered="false" title="进度管理" style="margin-bottom: 20px;height:100%">
        <template slot="extra">
          <a-button size="small" @click="criticalPathDisplayHandler">
            <template v-if="criticalPathDisplay">隐藏</template><template v-else>显示</template>关键路径
          </a-button>
          <a-button size="small" @click="settingBoxHandler">配置任务</a-button>
          <a-button size="small" @click="undo">撤销</a-button>
          <a-button size="small">保存数据</a-button>
        </template>
        <a-row>
          <a-col :sm="24" style="marginBottom: 10px">
            <Gantt  v-if='ganttDataLoaded'
                    :datas="ganttDatas"
                    ref="gantt"
                    style="height:75vh"></Gantt>
          </a-col>
        </a-row>
      </a-card>
      <a-drawer
        width="600px"
        placement="right"
        :closable="false"
        @close="onClose"
        :visible="settingBoxVisible"
      >
          <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '5px', paddingLeft: '16px'}">
            <a-tab-pane loading="true" tab="基本属性" key="1">
              <a-row>
                <a-col :span="24">
                  <a-table :columns="columns" :dataSource="data" :pagination="false">
                    <template
                      v-for="col in ['name', 'age']"
                      :slot="col"
                      slot-scope="text, record, index"
                    >
                      <div :key="col">
                        <a-input
                          v-if="record.editable && col !== 'name'"
                          style="margin: -5px 0"
                          :value="text"
                          @change="e => {log(record,text,index,col);handleChange(e.target.value, record.key, col)}"
                          @blur="() => save(record.key)"
                        />
                        <template v-else
                        ><span @dblclick="edit(record.key)">{{text}}</span></template>
                      </div>
                    </template>
                  </a-table>
                  <a-textarea placeholder="任务描述" :rows="4" style="margin-bottom: 20px"/>
                  <a-textarea placeholder="验收标准" :rows="4" style="margin-bottom: 80px"/>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="输入文档" key="2">
              <a-row>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">添加</a-button>
                    <a-button size="small">移除</a-button>
                    <a-button size="small">设置来源</a-button>
                  </div>
                  <a-table :columns="columns1" :dataSource="data1" :pagination="false" bordered>
                  </a-table>
                </a-col>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">打开</a-button>
                    <a-button size="small">新建文档</a-button>
                    <a-button size="small">添加文档</a-button>
                    <a-button size="small">移除文档</a-button>
                  </div>
                  <a-table :columns="columns2" :pagination="false" bordered>
                  </a-table>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="输出文档" key="3">
              <a-row>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">添加</a-button>
                    <a-button size="small">移除</a-button>
                    <a-button size="small">设置来源</a-button>
                  </div>
                  <a-table :columns="columns1" :dataSource="data1" :pagination="false" bordered>
                  </a-table>
                </a-col>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">打开</a-button>
                    <a-button size="small">新建文档</a-button>
                    <a-button size="small">添加文档</a-button>
                    <a-button size="small">移除文档</a-button>
                  </div>
                  <a-table :columns="columns2" :pagination="false" :scroll="{x:800}" bordered>
                  </a-table>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="日志" key="4">
              <a-row>
                <a-col :xl="16" :lg="12" :md="12" :sm="24" :xs="24">
                  xl-16 lg-12 md-12
                </a-col>
                <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
                  xl-8 lg-12 md-12
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="参考实例" key="5">
              <a-row>
                <a-col :xl="16" :lg="12" :md="12" :sm="24" :xs="24">
                  xl-16 lg-12 md-12
                </a-col>
                <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
                  xl-8 lg-12 md-12
                </a-col>
              </a-row>
            </a-tab-pane>
          </a-tabs>
        <div
          :style="{
          position: 'absolute',
          bottom: 0,
          width: '100%',
          borderTop: '1px solid #e8e8e8',
          padding: '10px 16px',
          textAlign: 'right',
          left: 0,
          background: '#fff',
          borderRadius: '0 0 4px 4px',
        }"
        >
          <a-button @click="onClose" type="primary">
            保存
          </a-button>
        </div>
      </a-drawer>
    </a-col>
  </a-row>


</div>
</template>

<script>
import Gantt from '@/components/Gantt/'
import DTree from '@/components/DTree/DTree'

const columns = [
  {
    dataIndex: 'name',
    title: '常规属性',
    width: '50%',
    scopedSlots: { customRender: 'name' },
  },
  {
    dataIndex: 'age',
    width: '50%',
    scopedSlots: { customRender: 'age' },
  },
];
const columns1 = [
  {
    dataIndex: 'type',
    title: '文档类型',
    width: '50%',
  },
  {
    dataIndex: 'from',
    title: '来源',
    width: '50%',
  },
];
const columns2 = [
  {
    dataIndex: 'index',
    title: '代号',
  },
  {
    dataIndex: 'name',
    title: '名称',
  },
  {
    dataIndex: 'type',
    title: '类型',
  },
  {
    dataIndex: 'version',
    title: '版本标识',
  },
  {
    dataIndex: 'status',
    title: '生效状态',
  },
  {
    dataIndex: 'vendor',
    title: '责任人',
  },
  {
    dataIndex: 'createTime',
    title: '创建日期',
  },
  {
    dataIndex: 'updateTime',
    title: '修改日期',
  },
  {
    dataIndex: 'from',
    title: '来源',
  },
]
const data = [], data1 = [], data2 = []
for (let i = 0; i < 5; i++) {
  data.push({
    key: i.toString(),
    name: `prop ${i}`,
    age: 32,
  });
  data1.push({
    key: i.toString(),
    type: `doc ${i}`,
    from: 'lib'
  });
}

export default {
  name: 'TaskManagement',
  components: { Gantt, DTree },
  data() {
    this.cacheData = data.map(item => ({ ...item }));
    return {
      data,
      columns,
      columns1,
      data1,
      columns2,
      treeDatas : [],
      treeDataLoaded: false,
      ganttDatas: [],
      ganttDataLoaded: false,
      criticalPathDisplay: false,
      settingBoxVisible: false,
    }
  },
  created() {
    fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/gantt")
      .then(resp => resp.json())
      .then(resp => {
        this.ganttDatas = resp;
        //数据获取后，初始化gantt图
      }).finally(() => {
      this.ganttDataLoaded = true;
    })
    fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/dhx_tree")
      .then(resp => resp.json())
      .then(resp => {
        this.treeDatas = resp;
      }).finally(() => {
      this.treeDataLoaded = true
    })
  },
  methods: {
    //点击tree的某项时触发
    treeAfterClickedCb (tree, id, e) {
      fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/gantt2")
        .then(resp => resp.json())
        .then(resp => {
          this.$refs.gantt.update(resp)
        })
    },
    settingBoxHandler(){
      this.settingBoxVisible = true
    },
    onClose() {
      this.settingBoxVisible = false;
    },
    //关键路径处理
    criticalPathDisplayHandler(){
      if (this.criticalPathDisplay) this.$refs.gantt.hideCriticalPath();
      else this.$refs.gantt.showCriticalPath();
      this.criticalPathDisplay = !this.criticalPathDisplay;
    },
    //撤销
    undo(){
      this.$refs.gantt.undo();
    },
    handleChange(value, key, column) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        target[column] = value;
        this.data = newData;
      }
    },
    edit(key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        target.editable = true;
        this.data = newData;
      }
    },
    save(key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        delete target.editable;
        this.data = newData;
        this.cacheData = newData.map(item => ({ ...item }));
      }
    },
    cancel(key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        Object.assign(target, this.cacheData.filter(item => key === item.key)[0]);
        delete target.editable;
        this.data = newData;
      }
    },
    log(record,text,index, v){
      console.log(record, 'record');
      console.log(text, 'text');
      console.log(index, 'index');
      console.log(v, 'v');
    }
  }

}
</script>

<style scoped lang="less">
</style>
