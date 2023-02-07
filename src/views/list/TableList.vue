<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48" align="middle">
            <a-col :md="8" :sm="24">
              <a-form-item label="姓名">
                <a-input ref="userNameInput" v-model="queryParam.userName" placeholder="用户名" @change="queryData">
                  <a-icon slot="prefix" type="user" />
                  <a-tooltip slot="suffix" title="输入查询的用户名">
                    <a-icon type="info-circle" style="color: rgba(0, 0, 0, 0.45)" />
                  </a-tooltip>
                </a-input>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="工号">
                <a-input ref="userNameInput" v-model="queryParam.userName" placeholder="工号" @change="queryData">
                  <a-icon slot="prefix" type="idcard" />
                  <a-tooltip slot="suffix" title="输入查询的用户名">
                    <a-icon type="info-circle" style="color: rgba(0, 0, 0, 0.45)" />
                  </a-tooltip>
                </a-input>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-space align="start">
                <a-button type="primary" @click="queryData">查询</a-button>
                <a-button @click="() => (this.queryParam = {})">重置</a-button>
              </a-space>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <a-table
        :columns="columns"
        :data-source="colData"
        :pagination="paginationOpt"
      >
        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="jumpOrderDetal(record.orderId)">查看工单</a>
          </template>
        </span>
      </a-table>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { orderList } from '@/api/manage'
// import pick from 'lodash.pick'
import StepByStepModal from './modules/StepByStepModal'
import AlertForm from './modules/CreateForm'

const columns = [
  {
    title: '工单发起',
    dataIndex: 'orderInitiate'
  },
  {
    title: '工单处理',
    dataIndex: 'orderDeal'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工程师工号',
    dataIndex: 'orderDealNo'
    // slots: { title: 'customTitle' },
    // scopedSlots: { customRender: 'userNo' }
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工程师电话',
    dataIndex: 'orderDealPhone'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工单状态',
    dataIndex: 'orderState'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '持续时长',
    dataIndex: 'orderDuration',
    // sorter: true,
    // needTotal: true,
    customRender: (text) => text + ' 小时'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '170px',
    scopedSlots: { customRender: 'action' }
  }
]

export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    AlertForm,
    StepByStepModal
  },
  data() {
    this.columns = columns
    return {
      headers: {
        authorization: 'authorization-text'
      },
      // create model
      visible: false,
      editVisible: false,
      confirmLoading: false,
      mdl: null,
      editMdl: {},
      // 高级搜索 展开/关闭
      advanced: true,
      // 查询参数
      queryParam: {},
      selectedRowKeys: [],
      selectedRows: [],
      ModalText: 'Content of the modal',
      delVisible: false,
      delConfirmLoading: false,
      // data,
      columns,
      loading: false,
      colData: [],
      departments: [],
      // 分页
      paginationOpt: {
        defaultCurrent: 1, // 默认当前页数
        defaultPageSize: 20, // 默认当前页显示数据的大小
        total: 0, // 总数，必须先有
        showSizeChanger: true,
        showQuickJumper: false,
        pageSizeOptions: ['10', '20', '30', '40'],
        showTotal: (total) => `共 ${total} 条`, // 显示总数
        onShowSizeChange: (current, pageSize) => {
          this.paginationOpt.defaultCurrent = 1
          this.paginationOpt.defaultPageSize = pageSize
          // this.getData()
        },
        // 改变每页数量时更新显示
        onChange: (current, size) => {
          this.paginationOpt.defaultCurrent = current
          this.paginationOpt.defaultPageSize = size
          // this.getData()
        }
      }
    }
  },
  created() {
    this.getData()
  },
  methods: {
    realIndex(index) {
      return index + 1
    },
    getData() {
      orderList().then((res) => {
        console.log('res', res.result.data)
        this.colData = res.result.data
        this.departments = res.result.departments
      })
    },

    toggleAdvanced() {
      this.advanced = !this.advanced
    },
    resetSearchForm() {
      this.queryParam = {
        date: moment(new Date())
      }
    },
    // 数据查询方法
    queryData() {
      console.log(this.queryParam)
      //   getData() {
      //   getServiceList().then((res) => {
      //     console.log('res', res.result.data)
      //     this.colData = res.result.data
      //     this.departments = res.result.departments
      //   })
      // },
    },
    // 新增用户
    // 批量删除
    // 单用户删除
    jumpOrderDetal(order) {
      console.log(order)
      // this.$router.push({ name: 'ProfileAdvanced' })
      this.$router.push({
        name: 'ProfileAdvanced',
        params: {
          keyId: order
        }
      })
    }
  }
}
</script>
