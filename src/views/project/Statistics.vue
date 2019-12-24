<template>
  <div>
    <a-row type="flex" :gutter="10">
      <a-col :sm="5" :xs="24">
        <a-card :bordered="false" style="margin-bottom: 20px;height:100%">
          <a-tabs size="small" :activeKey="tabKey" :tabBarGutter="5" @change="changeTab">
            <a-tab-pane tab="项目统计" key="1">
              <a-row :gutter="5">
                <a-col :sm="24" :xs="24">
                  <DTree v-if="treeDataLoaded"
                         :datas="treeDatas"
                         ref="tree"></DTree>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="资源统计" key="2">
              <a-row :gutter="5">
                <a-col :sm="24" :xs="24">
                  <DTree v-if="treeDataLoaded1"
                         :datas="treeDatas1"
                         ref="tree"></DTree>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="问题统计" key="3">
              <a-row :gutter="5">
                <a-col :sm="24" :xs="24">
                  <DTree v-if="treeDataLoaded2"
                         :datas="treeDatas2"
                         ref="tree"></DTree>
                </a-col>
              </a-row>
            </a-tab-pane>
          </a-tabs>
        </a-card>
      </a-col>
      <a-col :sm="19" :xs="24">
        <a-card :bordered="false" style="margin-bottom: 20px;height:85vh">
          <a-tabs size="small" v-if="tabKey==1">
            <a-tab-pane tab="进度分析" key="1">
              <div>
                <a-radio>总偏差率</a-radio>
                <a-radio>分布偏差率</a-radio>
              </div>
              <v-charts :options="pie_one" style="height: 600px;width: 1200px"></v-charts>
            </a-tab-pane>
            <a-tab-pane tab="状态统计" key="2">
              <a-row>
                <a-col :span="12">
                  <v-charts :options="pie_one" style="height: 400px;width: 600px"></v-charts>
                </a-col>
                <a-col :span="12">
                  <a-table :columns="columns" :dataSource="data" bordered></a-table>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="工作量统计" key="3">Content of tab 3</a-tab-pane>
            <a-tab-pane tab="问题统计" key="4">Content of tab 3</a-tab-pane>
            <a-tab-pane tab="项目清单" key="5">Content of tab 3</a-tab-pane>
          </a-tabs>
          <template v-if="tabKey==2">
            <div slot="title">资源统计</div>
            <a-button size="small" slot="extra">刷新</a-button>
            <div style="margin-bottom: 10px">
              <label style="color:rgba(0, 0, 0, 0.85);margin-right: 10px">时间过滤 </label>
              <a-radio>本周</a-radio>
              <a-radio>本月</a-radio>
              <a-radio>本季度</a-radio>
              <a-radio>本年度</a-radio>
              <a-radio>指定时间段</a-radio>
              <a-range-picker @change="onChange" />
            </div>
            <div>
              <label style="color:rgba(0, 0, 0, 0.85);margin-right: 10px">状态过滤 </label>
              <a-checkbox>未下达</a-checkbox>
              <a-checkbox>等待中</a-checkbox>
              <a-checkbox>未启动</a-checkbox>
              <a-checkbox>执行中</a-checkbox>
              <a-checkbox>待确认</a-checkbox>
              <a-checkbox>已完成</a-checkbox>
              <a-checkbox>冻结</a-checkbox>
            </div>
            <div style="margin-top: 30px;margin-left: -15px">
              <a-tabs defaultActiveKey="1">
                <a-tab-pane tab="部门工作量统计" key="1">
                  <div style="text-align: center;height: 500px;width: 100%">
                  <v-charts :options="pie_one" style="width: 100%"></v-charts>
                  </div>
                </a-tab-pane>
                <a-tab-pane tab="任务资源列表" key="2">
                  <a-table :columns="columns" :dataSource="data" bordered></a-table>
                </a-tab-pane>
              </a-tabs>
            </div>
          </template>
          <template v-if="tabKey==3">
            <div>
              <a-tabs defaultActiveKey="1" size="small">
                <a-tab-pane tab="问题分布图" key="1">
                  <a-row>
                    <a-col :span="12">
                      <div style="text-align: center;height: 500px;width: 100%">
                        <v-charts :options="pie_one" style="width: 100%"></v-charts>
                      </div>
                    </a-col>
                    <a-col :span="12">
                      <div style="text-align: center;height: 500px;width: 100%">
                        <v-charts :options="pie_one" style="width: 100%"></v-charts>
                      </div>
                    </a-col>
                  </a-row>
                </a-tab-pane>
                <a-tab-pane tab="问题列表" key="2">
                  <div style="text-align: right;margin-bottom: 10px">
                    <a-button size="small">刷新</a-button>
                    <a-button size="small">打开</a-button>
                  </div>
                  <a-table :columns="columns" :dataSource="data" bordered></a-table>
                </a-tab-pane>
              </a-tabs>
            </div>
          </template>
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>

<script>
  import DTree from '@/components/DTree/DTree'
  import ECharts from 'vue-echarts'
  import 'echarts/lib/chart/line'
  import 'echarts/lib/chart/pie'
  import 'echarts/lib/chart/bar'

  const columns = [
    {
      dataIndex: 'name',
      title: '名称',
      scopedSlots: { customRender: 'name' },
    },
    {
      dataIndex: 'status',
      title: '状态',
      scopedSlots: { customRender: 'status' },
    },
    {
      dataIndex: 'department',
      title: '所属部门',
    },
    {
      dataIndex: 'type',
      title: '类型',
    },
    {
      dataIndex: 'vendor',
      title: '负责人',
    },
  ];

  const pie_one = {
    title : {
      text: '某站点用户访问来源',
      subtext: '纯属虚构',
      x:'center'
    },
    tooltip : {
      trigger: 'item',
      formatter: "{a} <br/>{b} : {c} ({d}%)"
    },
    legend: {
      orient: 'vertical',
      left: 'left',
      data: ['偏差1','偏差2','偏差3']
    },
    series : [
      {
        name: '访问来源',
        type: 'pie',
        radius : '55%',
        center: ['50%', '60%'],
        data:[
          {value:335, name:'偏差1'},
          {value:234, name:'偏差2'},
          {value:1500, name:'偏差3'},
        ],
        itemStyle: {
          emphasis: {
            shadowBlur: 10,
            shadowOffsetX: 0,
            shadowColor: 'rgba(0, 0, 0, 0.5)'
          }
        }
      }
    ]
  };

  export default {
    name: 'Statistics',
    data () {
      return {
        treeDatas: [],
        treeDataLoaded: false,
        treeDatas1: [],
        treeDataLoaded1: false,
        treeDatas2: [],
        treeDataLoaded2: false,
        tabKey: "1",

        pie_one,
        columns,
      }
    },
    components: {
      DTree,
      "v-charts" : ECharts,
    },
    created(){
      fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/dhx_tree3")
        .then(resp => resp.json())
        .then(resp => {
          this.treeDatas = resp;
        }).finally(() => {
        this.treeDataLoaded = true
      })
      fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/dhx_tree4")
        .then(resp => resp.json())
        .then(resp => {
          this.treeDatas1 = resp;
        }).finally(() => {
        this.treeDataLoaded1 = true
      })
      fetch("https://www.fastmock.site/mock/7f143c4ab35b8dbc46edbcc32c83547a/ty/dhx_tree5")
        .then(resp => resp.json())
        .then(resp => {
          this.treeDatas2 = resp;
        }).finally(() => {
        this.treeDataLoaded2 = true
      })
    },
    methods: {
      changeTab(akey) {
        this.tabKey = akey;
      }
    }
  }
</script>

<style scoped>

</style>