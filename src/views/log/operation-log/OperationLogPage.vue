<template>
  <div class="ant-pro-page-container-children-content">
    <!-- 查询条件 -->
    <div class="ant-pro-table-search">
      <a-form v-bind="searchFormLayout">
        <a-row :gutter="16">
          <a-col :xl="8" :md="12" :sm="24">
            <a-form-item label="追踪ID">
              <a-input v-model="queryParam.traceId" placeholder="请输入" />
            </a-form-item>
          </a-col>
          <a-col :xl="8" :md="12" :sm="24">
            <a-form-item label="请求IP">
              <a-input v-model="queryParam.ip" placeholder="请输入" />
            </a-form-item>
          </a-col>
          <template v-if="advanced">
            <a-col :xl="8" :md="12" :sm="24">
              <a-form-item label="请求URI">
                <a-input v-model="queryParam.uri" placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :xl="8" :md="12" :sm="24">
              <a-form-item label="日志消息">
                <a-input v-model="queryParam.msg" placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :xl="10" :md="12" :sm="24">
              <a-form-item label="访问时间">
                <a-range-picker
                  :value="searchTimeValue"
                  show-time
                  :placeholder="['Start Time', 'End Time']"
                  format="YYYY-MM-DD HH:mm:ss"
                  @change="onTimeChange"
                />
              </a-form-item>
            </a-col>
          </template>

          <a-col
            :xl="6"
            :md="12"
            :sm="24"
            class="table-page-search-wrapper"
          >
            <div class="table-page-search-submitButtons">
              <a-button type="primary" @click="reloadTable">查询</a-button>
              <a-button style="margin-left: 8px" @click="resetSearchForm">重置</a-button>
              <a style="margin-left: 8px" @click="toggleAdvanced">
                {{ advanced ? '收起' : '展开' }}
                <a-icon :type="advanced ? 'up' : 'down'" />
              </a>
            </div>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <a-card :bordered="false" :body-style="{paddingTop: 0, paddingBottom: 0}">
      <!-- 操作按钮区域 -->
      <div class="ant-pro-table-toolbar">
        <div class="ant-pro-table-toolbar-title">操作日志</div>
      </div>
      <div class="ant-pro-table-wrapper">
        <!--数据表格-->
        <a-table
          ref="table"
          size="middle"
          :row-key="rowKey"
          :columns="columns"
          :data-source="dataSource"
          :pagination="pagination"
          :loading="loading"
          :scroll="{x : 1100}"
          @change="handleTableChange"
        >
          <template #type-slot="text">
            <dict-slot dict-code="operation_type" :value="text" />
          </template>
          <template #status-slot="text">
            <dict-slot dict-code="log_status" :value="text" />
          </template>
          <template #expandedRowRender="record">
            <p>params:</p>
            <pre><div class="wordwrap" v-html="record.params" /></pre>
            <p>userAgent:</p>
            {{ record.userAgent }}}
          </template>
        </a-table>
      </div>
    </a-card>
  </div>
</template>

<script>
import { getPage } from '@/api/log/operation-log'
import { TablePageMixin } from '@/mixins'

export default {
  name: 'OperationLogPage',
  mixins: [TablePageMixin],
  data () {
    return {
      getPage: getPage,

      columns: [
        {
          title: '追踪ID',
          dataIndex: 'traceId',
          width: '205px',
        },
        {
          title: '日志消息',
          dataIndex: 'msg',
          ellipsis: true,
          width: '120px',
        },
        {
          title: '类型',
          dataIndex: 'type',
          align: 'center',
          scopedSlots: { customRender: 'type-slot' }
        },
        {
          title: '请求IP',
          dataIndex: 'ip',
          width: '120px'
        },
        {
          title: '请求URI',
          dataIndex: 'uri',
          ellipsis: true
        },
        {
          title: '方法',
          dataIndex: 'method'
        },
        {
          title: '耗时',
          dataIndex: 'time',
          customRender: function (text) {
            return text + ' ms'
          }
        },
        {
          title: '操作人',
          dataIndex: 'operator'
        },
        {
          title: '状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status-slot' }
        },
        {
          title: '创建时间',
          dataIndex: 'createTime',
          width: '150px',
          sorter: true
        }
      ],

      // 查询时间
      searchTimeValue: []
    }
  },
  methods: {
    onTimeChange (dates, dateStrings) {
      this.searchTimeValue = dateStrings
      this.queryParam.startTime = dateStrings[0]
      this.queryParam.endTime = dateStrings[1]
    },
    // 清空搜索条件
    resetSearchForm () {
      this.queryParam = {}
      this.searchTimeValue = []
    }
  }
}
</script>

<style>
</style>
