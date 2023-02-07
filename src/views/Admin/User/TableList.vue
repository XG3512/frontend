<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="8" :sm="24">
              <!-- <a-form-item label="规则编号">
                <a-input v-model="queryParam.id" placeholder="" />
              </a-form-item> -->
              <a-form-item label="账号角色">
                <a-select v-model="queryParam.role" placeholder="请选择" default-value="0" @change="queryData">
                  <a-select-option value="0">管理员</a-select-option>
                  <a-select-option value="1">报修用户</a-select-option>
                  <a-select-option value="2">维修工程师</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="所属部门">
                <a-select v-model="queryParam.department" placeholder="请选择" default-value="0" @change="queryData">
                  <a-select-option v-for="item in departments" :key="item">{{ item }}</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="用户姓名">
                  <a-input ref="userNameInput" v-model="queryParam.userName" placeholder="用户名" @change="queryData">
                    <a-icon slot="prefix" type="user" />
                    <a-tooltip slot="suffix" title="输入查询的用户名">
                      <a-icon type="info-circle" style="color: rgba(0, 0, 0, 0.45)" />
                    </a-tooltip>
                  </a-input>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="创建日期">
                  <a-date-picker v-model="queryParam.date" style="width: 100%" placeholder="请输入更新日期" />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="电话号码">
                  <a-input
                    ref="userPhoneInput"
                    v-model="queryParam.userPhone"
                    placeholder="手机号"
                    @pressEnter="queryData"
                  >
                    <a-icon slot="prefix" type="phone" />
                    <a-tooltip slot="suffix" title="输入查询的手机号">
                      <a-icon type="info-circle" style="color: rgba(0, 0, 0, 0.45)" />
                    </a-tooltip>
                  </a-input>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="用户工号">
                  <a-input ref="userNoInput" v-model="queryParam.userNo" placeholder="工号" @pressEnter="queryData">
                    <a-icon slot="prefix" type="idcard" />
                    <a-tooltip slot="suffix" title="输入查询的手机号">
                      <a-icon type="info-circle" style="color: rgba(0, 0, 0, 0.45)" />
                    </a-tooltip>
                  </a-input>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24"> </a-col>
            </template>
            <a-col :md="(!advanced && 8) || 24" :sm="24">
              <span
                class="table-page-search-submitButtons"
                :style="(advanced && { float: 'right', overflow: 'hidden' }) || {}"
              >
                <a-space size="10" v-if="advanced">
                  <a-button type="primary">导出所查</a-button>
                  <div style="width: 10px"></div>
                  <a-button type="primary">导出全部</a-button>
                  <div style="width: 10px"></div>
                </a-space>
                <a-button type="primary" @click="queryData">查询</a-button>
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
        <a-row type="flex" justify="space-between">
          <a-col>
            <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
            <!-- eslint-disable-next-line vue/max-attributes-per-line -->
            <a-button type="danger" icon="delete" @click="delShowModal" v-if="selectedRowKeys.length > 0"
              >批量删除</a-button
            >
          </a-col>
          <a-col :span="4" style="min-width: 300px">
            <!-- 批量导入用户 -->
            <a-button type="link">点击下载导入示例</a-button>
            <a-upload
              name="file"
              :multiple="true"
              action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
              :headers="headers"
              @change="handleChange"
            >
              <a-button> <a-icon type="upload" /> 批量新增 </a-button>

              <!-- 批量导入用户 -->
            </a-upload>
          </a-col>

          <a-modal
            title="是否批量删除如下账号⚠️"
            :visible="delVisible"
            :confirm-loading="delConfirmLoading"
            @ok="delHandleOk"
            @cancel="delHandleCancel"
          >
            <div v-for="(item, index) in selectedRowKeys" :key="item">
              <a-descriptions :title="realIndex(index)">
                <a-descriptions-item label="姓名"> {{ colData[item - 1]['userName'] }} </a-descriptions-item>
                <a-descriptions-item label="角色"> {{ colData[item - 1]['userRole'] }} </a-descriptions-item>

                <a-descriptions-item label="工号"> {{ colData[item - 1]['userNo'] }} </a-descriptions-item>
                <a-descriptions-item label="电话"> {{ colData[item - 1]['userPhone'] }} </a-descriptions-item>
                <a-descriptions-item label="部门"> {{ colData[item - 1]['userDepartment'] }} </a-descriptions-item>
              </a-descriptions>
            </div>
          </a-modal>
        </a-row>
      </div>
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
      <alert-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
        alertMessage="新增用户,密码默认为Abc@工号,请通知用户尽快更改"
      />
      <alert-form
        ref="editCreateModal"
        :visible="editVisible"
        :loading="confirmLoading"
        :model="editMdl"
        @cancel="editHandleCancel"
        @ok="editHandleOk"
        :inputDisable="false"
        alertTitle="修改信息"
        alertMessage="修改用户信息后，密码默认重置为Abc@工号"
      />
      <step-by-step-modal ref="modal" @ok="handleOk" />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getServiceList } from '@/api/manage'
import pick from 'lodash.pick'
import StepByStepModal from './modules/StepByStepModal'
import AlertForm from './modules/CreateForm'

const columns = [
  {
    title: '用户角色',
    dataIndex: 'userRole'
  },
  {
    title: '姓名',
    dataIndex: 'userName'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工号',
    dataIndex: 'userNo'
    // slots: { title: 'customTitle' },
    // scopedSlots: { customRender: 'userNo' }
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '电话',
    dataIndex: 'userPhone'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '部门',
    dataIndex: 'userDepartment'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '工单次数',
    dataIndex: 'userOrderNum',
    // sorter: true,
    // needTotal: true,
    customRender: (text) => text + ' 次'
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
      advanced: false,
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
        defaultPageSize: 10, // 默认当前页显示数据的大小
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
  computed: {},
  methods: {
    realIndex(index) {
      return index + 1
    },
    handleChange(info) {
      if (info.file.status !== 'uploading') {
        console.log(info.file, info.fileList)
      }
      if (info.file.status === 'done') {
        this.$message.success(`${info.file.name} file uploaded successfully`)
      } else if (info.file.status === 'error') {
        this.$message.error(`${info.file.name} file upload failed.`)
      }
    },
    // 表单数据更新方法
    getData() {
      getServiceList().then((res) => {
        console.log('res', res.result.data)
        this.colData = res.result.data
        this.departments = res.result.departments
      })
    },
    delShowModal() {
      this.delVisible = true
      console.log('rowkey', this.selectedRowKeys, 'roes', this.selectedRows)
    },
    delHandleOk(e) {
      this.delUsers()
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
      const fields = ['userRole', 'userName', 'userPhone', 'userNo', 'id']
      console.log('record', record)
      this.editMdl.id = record.userNo
      this.editMdl.userName = record.userName
      this.editMdl.userPhone = record.userPhone
      this.editMdl.userRole = record.userRole
      this.editMdl.userNo = record.userNo
      console.log('editMdl', this.editMdl)
      this.$refs.editCreateModal.form.setFieldsValue(pick(this.editMdl, fields))
      this.editVisible = true
      // this.mdl = { ...record }
    },
    handleOk() {
      const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values, this.formData)
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
              // this.$refs.table.refresh()
              this.creatNewUser(values)
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
    editHandleCancel() {
      this.editVisible = false

      const form = this.$refs.editCreateModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    editHandleOk() {
      const form = this.$refs.editCreateModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values, this.formData)
          if (values.id > 0) {
            // 修改 e.g.
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then((res) => {
              this.editVisible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.editUser(values)

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then((res) => {
              this.editVisible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              // this.$refs.table.refresh()
              this.editUser(values)
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleSub(record) {
      if (record.status !== 0) {
        this.delUser(record.userNo)
        this.$message.info(`账号${record.userNo} 删除成功`)
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
      var selectedRow = []
      for (var i in selectedRowKeys) {
        selectedRow.push(this.colData[selectedRowKeys[i] - 1]['userNo'])
      }
      this.selectedRows = selectedRow
      console.log(this.selectedRows)
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
    creatNewUser(userInfo) {
      console.log('userinof', userInfo)
      this.$message.info('新增成功')
      this.getData()
      //       {
      //     "userRole": "2",
      //     "userName": "444444444",
      //     "userPhone": "444444444444",
      //     "userNo": "444444444444",
      //     "id": 0
      //        }
    },
    // 批量删除
    delUsers() {
      console.log(this.selectedRows)
      this.$message.info('批量删除成功')
      this.getData()
      //       [ userNo
      //     97068983,
      //     200465041,
      //     201382889
      // ]
    },
    // 单用户删除
    delUser(user) {
      console.log(user)
      this.getData()
      // 24822107 userNo
    },
    // 更改用户信息
    editUser(user) {
      console.log('change', user)
      this.getData()
      //       {id为useNo
      //     "userRole": "报修用户",
      //     "userName": "潘超",
      //     "userPhone": "4060547156399999",
      //     "id": 73323107
      // }
    }
  }
}
</script>
