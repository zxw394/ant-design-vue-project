<template>
  <div>
    <a-row type="flex" :gutter="10">
      <a-col :sm="4" :xs="24">
        <a-card :bordered="false" title="我的项目任务" style="margin-bottom: 20px;height:100%">
          <a-row :gutter="5">
            <a-col :sm="24" :xs="24">
              <DTree v-if="treeDataLoaded"
                     :datas="treeDatas"
                     ref="tree"></DTree>
            </a-col>
          </a-row>
        </a-card>
      </a-col>
      <a-col :sm="20" :xs="24">
        <a-card :bordered="false" title="任务列" style="margin-bottom: 20px;height:100%">
          <template slot="extra">
            <a-button size="small">刷新</a-button>
            <a-button size="small">查看提醒</a-button>
          </template>
          <a-row>
            <a-col :sm="24" style="marginBottom: 10px">
              <a-table :columns="columns" :dataSource="data" bordered
                       style="height:75vh" :scroll="{ x: 1600}">
                <a slot="name" slot-scope="text, record, index">{{text}}</a>
                <div slot="status" slot-scope="text, record, index" style="display: flex;align-items: center;justify-content: space-around">
                  <status-light :status="text ? 'green' : 'red'" style="height: 21px"/><div>{{text ? '执行中' : '已停止'}}</div>
                </div>
                <template
                  slot="progress"
                  slot-scope="text, record, index"
                >
                  <div key="progress">
                    <a-progress :percent="!record.status ? 100 : record.progress" :status="!record.status ? 'exception' : 'active'"/>
                  </div>
                </template>
                <div slot="action" slot-scope="text" >
                  <a href="javascript:;" style="margin-right: 5px">冻结</a>
                  <a href="javascript:;" style="margin-right: 5px">状态分析</a>
                  <a href="javascript:;" style="margin-right: 5px" @click="settingBoxHandler">任务配置</a>
                </div>
              </a-table>
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
                  <a-table :columns="columns1" :dataSource="data1" :pagination="false">
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
            <a-tab-pane tab="参考实例" key="2">
              <a-row>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">添加</a-button>
                    <a-button size="small">移除</a-button>
                    <a-button size="small">设置来源</a-button>
                  </div>
                  <a-table :pagination="false" bordered>
                  </a-table>
                </a-col>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">打开</a-button>
                    <a-button size="small">新建文档</a-button>
                    <a-button size="small">添加文档</a-button>
                    <a-button size="small">移除文档</a-button>
                  </div>
                  <a-table :pagination="false" bordered>
                  </a-table>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="启动/完成条件" key="3">
              <a-row>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">添加</a-button>
                    <a-button size="small">移除</a-button>
                    <a-button size="small">设置来源</a-button>
                  </div>
                  <a-table :pagination="false" bordered>
                  </a-table>
                </a-col>
                <a-col :span="24" style="margin-bottom: 10px">
                  <div style="margin-bottom: 10px;text-align: right;margin-top: 15px">
                    <a-button size="small">打开</a-button>
                    <a-button size="small">新建文档</a-button>
                    <a-button size="small">添加文档</a-button>
                    <a-button size="small">移除文档</a-button>
                  </div>
                  <a-table  :pagination="false" :scroll="{x:800}" bordered>
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
  import DTree from '@/components/DTree/DTree'
  import StatusLight from '@/components/Common/StatusLight'
const columns = [
  {
    dataIndex: 'name',
    title: '名称',
    width: 150,
    scopedSlots: { customRender: 'name' },
  },
  {
    dataIndex: 'status',
    title: '状态',
    width: 200,
    scopedSlots: { customRender: 'status' },
  },
  {
    dataIndex: 'milestone',
    title: '所属里程碑',
    width: 150
  },
  {
    dataIndex: 'type',
    title: '类型',
    width: 150
  },
  {
    dataIndex: 'priority',
    title: '优先级',
    width: 150
  },
  {
    dataIndex: 'endTime',
    title: '结束日期',
    width: 200
  },
  {
    dataIndex: 'progress',
    title: '进度',
    width: 300,
    scopedSlots: { customRender: 'progress' },
  },
  {
    dataIndex: 'vendor',
    title: '负责人',
    width: 150,
  },
  {
    title: '操作',
    // key: 'operation',
    fixed: 'right',
    width: 200,
    align: 'center',
    scopedSlots: { customRender: 'action' },
  },
];
const columns1 = [
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
const data = [], data1 = [];
let initStatus = true
for (let i = 0; i < 40; i++) {
  data.push({
    key: i.toString(),
    milestone: `milestone ${i}`,
    name: `name ${i}`,
    type: `大型项目 ${i}`,
    priority: `高`,
    progress:  32 + i,
    status: initStatus = !initStatus ? true : false,
    endTime: '2019-08-05',
    vendor: `nick ${i}`
  });
}
for (let i = 0; i < 5; i++) {
  data1.push({
    key: i.toString(),
    name: `prop ${i}`,
    age: 32,
  });
}
export default {
  name: 'MyTask',
  data () {
    return {
      data,
      data1,
      columns,
      columns1,
      treeDatas : [],
      treeDataLoaded: false,
      settingBoxVisible: false,
    }
  },
  created () {
    fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/dhx_tree2")
      .then(resp => resp.json())
      .then(resp => {
        this.treeDatas = resp;
      }).finally(() => {
      this.treeDataLoaded = true
    })
  },
  components: {
    DTree,
    StatusLight,
  },
  methods: {
    settingBoxHandler(){
      this.settingBoxVisible = true
    },
    onClose() {
      this.settingBoxVisible = false;
    },
    handleChange(value, key, column) {
      const newData = [...this.data1];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        target[column] = value;
        this.data1 = newData;
      }
    },
    edit(key) {
      const newData = [...this.data1];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        target.editable = true;
        this.data1 = newData;
      }
    },
    save(key) {
      const newData = [...this.data1];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        delete target.editable;
        this.data1 = newData;
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

<style scoped>

</style>
