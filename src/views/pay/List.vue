<template>
  <div class="ant-pro-page-container-children-content">
    <!-- 查询条件 -->
    <div class="ant-pro-table-search">
      <a-form v-bind="searchFormLayout">
        <a-row :gutter="16">
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="交易号">
              <a-input v-model="queryParam.tradeNo" placeholder="请输入"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="项目ID">
              <a-input-number v-model="queryParam.projectId" placeholder="请输入" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="项目交易号">
              <a-input v-model="queryParam.projectTradeNo" placeholder="请输入"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="第三方交易号">
              <a-input v-model="queryParam.thirdPartTradeNo" placeholder="请输入"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="支付状态">
              <dict-select dict-code="pay_status" placeholder="支付状态" v-model="queryParam.disabled"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="支付货币">
              <dict-select dict-code="currency" placeholder="支付货币" v-model="queryParam.disabled"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="支付模式">
              <dict-select dict-code="pay_mode" placeholder="支付模式" v-model="queryParam.disabled"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :md="12" :sm="24">
            <a-form-item label="通知状态">
              <dict-select dict-code="notify_status" placeholder="通知状态" v-model="queryParam.disabled"/>
            </a-form-item>
          </a-col>


          <!-- <template v-if="advanced">
           </template>-->

          <a-col
            :xl="6"
            :md="12"
            :sm="24"
            class="table-page-search-wrapper"
          >
            <div class="table-page-search-submitButtons">
              <a-button type="primary" @click="reloadTable">查询</a-button>
              <a-button style="margin-left: 8px" @click="resetSearchForm">重置</a-button>
            </div>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <a-card :bordered="false" :body-style="{paddingTop: '0'}">

      <!--数据表格区域-->
      <div class="ant-pro-table-wrapper">
        <a-table
          ref="table"
          size="middle"
          :row-key="rowKey"
          :columns="columns"
          :data-source="dataSource"
          :pagination="pagination"
          :loading="loading"
          @change="handleTableChange"
        >
          <template #status-slot="text">
            <dict-slot dict-code="pay_status" :value="text"/>
          </template>
          <template #currency-slot="text">
            <dict-slot dict-code="currency" :value="text"/>
          </template>
          <template #chain-slot="text">
            <dict-slot dict-code="chain" :value="text"/>
          </template>
          <template #third-part-slot="text">
            <dict-slot dict-code="third_part" :value="text"/>
          </template>
          <template #mode-slot="text">
            <dict-slot dict-code="pay_mode" :value="text"/>
          </template>
          <template #notify-status-slot="text">
            <dict-slot dict-code="notify_status" :value="text"/>
          </template>
          <template #action-slot="text, record">
            <a v-has="'project:read'" @click="$refs.log.show(record)">通知记录</a>
          </template>
        </a-table>
      </div>
    </a-card>

    <logs ref="log"/>
  </div>
</template>

<script>
import {list} from '@/api/platform/pay'
import {TablePageMixin} from '@/mixins'
import Logs from './Logs'
import {littleCamelToUnderline} from "@/utils/strUtil";

export default {
  name: 'ProjectList',
  components: {Logs},
  mixins: [TablePageMixin],
  data() {
    return {
      getPage: list,
      columns: [
        {
          title: '交易号',
          dataIndex: 'tradeNo',
        },
        {
          title: '所属项目',
          dataIndex: 'projectId',
          width: 50,
        },
        {
          title: '项目交易号',
          dataIndex: 'projectTradeNo',
          ellipsis: true
        },
        {
          title: '第三方交易号',
          dataIndex: 'thirdPartTradeNo',
          ellipsis: true
        },
        {
          title: '支付状态',
          dataIndex: 'status',
          width: 100,
          scopedSlots: {customRender: 'status-slot'}
        },
        {
          title: '支付金额',
          dataIndex: 'amount',
        },
        {
          title: '支付货币',
          dataIndex: 'currency',
          width: 50,
          scopedSlots: {customRender: 'currency-slot'}
        },
        {
          title: '链',
          dataIndex: 'chain',
          width: 50,
          scopedSlots: {customRender: 'chain-slot'}
        },
        {
          title: '收款地址',
          dataIndex: 'address',
          ellipsis: true
        },
        {
          title: '重试截止时间',
          dataIndex: 'retryEndTime'
        },
        {
          title: '第三方',
          dataIndex: 'thirdPart',
          width: 50,
          scopedSlots: {customRender: 'third-part-slot'}
        },
        {
          title: '支付模式',
          dataIndex: 'mode',
          width: 100,
          scopedSlots: {customRender: 'mode-slot'}
        },
        {
          title: '描述',
          dataIndex: 'desc',
          ellipsis: true
        },
        {
          title: '通知状态',
          dataIndex: 'notifyStatus',
          width: 100,
          scopedSlots: {customRender: 'notify-status-slot'}
        },
        {
          title: '完成时间',
          dataIndex: 'completeTime',
          width: 150
        },
        {
          title: '创建时间',
          dataIndex: 'createTime',
          width: 150
        },
        {
          title: '操作',
          align: 'center',
          width: 100,
          fixed: 'right',
          scopedSlots: {customRender: 'action-slot'}
        }
      ],
    }
  },
  created() {
  },
  methods: {
    initDefaultSort() {
      this.sortField = littleCamelToUnderline('tradeNo')
      this.sortOrder = 'desc'
    },
  }
}
</script>
<style scoped>
.menu-tree-table >>> td {
  white-space: nowrap !important;
}

.ant-tag.single-tag {
  margin-right: 0
}
</style>
