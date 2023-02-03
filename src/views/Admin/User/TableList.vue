<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="8" :sm="24">
              <a-form-item label="规则编号">
                <a-input v-model="queryParam.id" placeholder="" />
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="使用状态">
                <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                  <a-select-option value="0">全部</a-select-option>
                  <a-select-option value="1">关闭</a-select-option>
                  <a-select-option value="2">运行中</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="调用次数">
                  <a-input-number v-model="queryParam.callNo" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="更新日期">
                  <a-date-picker v-model="queryParam.date" style="width: 100%" placeholder="请输入更新日期" />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="使用状态">
                  <a-select v-model="queryParam.useStatus" placeholder="请选择" default-value="0">
                    <a-select-option value="0">全部</a-select-option>
                    <a-select-option value="1">关闭</a-select-option>
                    <a-select-option value="2">运行中</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="使用状态">
                  <a-select placeholder="请选择" default-value="0">
                    <a-select-option value="0">全部</a-select-option>
                    <a-select-option value="1">关闭</a-select-option>
                    <a-select-option value="2">运行中</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
            </template>
            <a-col :md="(!advanced && 8) || 24" :sm="24">
              <span
                class="table-page-search-submitButtons"
                :style="(advanced && { float: 'right', overflow: 'hidden' }) || {}"
              >
                <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
                <a-button style="margin-left: 8px" @click="() => (this.queryParam = {})">重置</a-button>
                <a @click="toggleAdvanced" style="margin-left: 8px">
                  {{ advanced ? '收起' : '展开' }}
                  <a-icon :type="advanced ? 'up' : 'down'" />
                </a>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>

      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
        <!-- eslint-disable-next-line vue/max-attributes-per-line -->
        <a-button type="danger" icon="delete" @click="delShowModal" v-if="selectedRowKeys.length > 0"
          >批量删除</a-button
        >
        <a-modal
          title="是否批量删除如下账号⚠️"
          :visible="delVisible"
          :confirm-loading="delConfirmLoading"
          @ok="delHandleOk"
          @cancel="delHandleCancel"
        >
          <p>{{ ModalText }}</p>
        </a-modal>
      </div>

      <!-- <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data="loadData"
        :alert="true"
        :rowSelection="rowSelection"

      >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
        <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
        </span>
        <span slot="description" slot-scope="text">
          <ellipsis :length="4" tooltip>{{ text }}</ellipsis>
        </span>

        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleEdit(record)">更改信息</a>
            <a-divider type="vertical" />
            <a @click="handleSub(record)">删除账号</a>
          </template>
        </span>
      </s-table> -->
      <a-table
        :row-selection="{ selectedRowKeys: selectedRowKeys, onChange: onSelectChange }"
        :columns="columns"
        :data-source="colData"
        :pagination="paginationOpt"
      >
        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleEdit(record)">更改信息</a>
            <a-divider type="vertical" />
            <a @click="handleSub(record)">删除账号</a>
          </template>
        </span>
      </a-table>
      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
      />
      <step-by-step-modal ref="modal" @ok="handleOk" />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'

const columns = [
  {
    title: '用户角色',
    dataIndex: 'no'
  },
  {
    title: '姓名',
    dataIndex: 'descriptio'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '电话',
    dataIndex: 'descripti'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '部门',
    dataIndex: 'description'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工单次数',
    dataIndex: 'callNo',
    sorter: true,
    needTotal: true,
    customRender: (text) => text + ' 次'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '170px',
    scopedSlots: { customRender: 'action' }
  }
]

// const statusMap = {
//   0: {
//     status: 'default',
//     text: '休息'
//   },
//   1: {
//     status: 'processing',
//     text: '工作中'
//   },
//   2: {
//     status: 'success',
//     text: '待派单'
//   },
//   3: {
//     status: 'error',
//     text: '上课中'
//   }
// }

export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    StepByStepModal
  },
  data() {
    this.columns = columns
    return {
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      // loadData: () => {
      //   // const requestParameters = Object.assign({}, parameter, this.queryParam)
      //   // console.log('loadData request parameters:', requestParameters)
      //   getServiceList().then((res) => {
      //     return res.result
      //   })
      // },
      selectedRowKeys: [],
      // selectedRows: [],
      ModalText: 'Content of the modal',
      delVisible: false,
      delConfirmLoading: false,
      // data,
      columns,
      loading: false,
      colData: [],
      // 分页
      paginationOpt: {
        defaultCurrent: 1, // 默认当前页数
        defaultPageSize: 10, // 默认当前页显示数据的大小
        total: 0, // 总数，必须先有
        showSizeChanger: true,
        showQuickJumper: false,
        pageSizeOptions: ['5', '10', '15', '20'],
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
    getData() {
      getServiceList().then((res) => {
        console.log('res', res.result.data)
        this.colData = res.result.data
      })
    },
    delShowModal() {
      this.delVisible = true
      console.log('rowkey', this.selectedRowKeys, 'roes', this.selectedRows)
    },
    delHandleOk(e) {
      this.ModalText = 'The modal will be closed after two seconds'
      this.confirmLoading = true
      setTimeout(() => {
        this.delVisible = false
        this.delConfirmLoading = false
      }, 500)
    },
    delHandleCancel(e) {
      console.log('Clicked cancel button')
      this.delVisible = false
    },
    handleAdd() {
      this.mdl = null
      this.visible = true
    },
    handleEdit(record) {
      this.visible = true
      this.mdl = { ...record }
    },
    handleOk() {
      const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          if (values.id > 0) {
            // 修改 e.g.
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then((res) => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then((res) => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel() {
      this.visible = false

      const form = this.$refs.createModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    handleSub(record) {
      if (record.status !== 0) {
        this.$message.info(`${record.no} 订阅成功`)
      } else {
        this.$message.error(`${record.no} 订阅失败，规则已关闭`)
      }
    },
    // onSelectChange(selectedRowKeys, selectedRows) {
    //   this.selectedRowKeys = selectedRowKeys
    //   this.selectedRows = selectedRows
    // },
    onSelectChange(selectedRowKeys) {
      console.log('selectedRowKeys changed: ', selectedRowKeys)
      this.selectedRowKeys = selectedRowKeys
    },
    toggleAdvanced() {
      this.advanced = !this.advanced
    },
    resetSearchForm() {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
