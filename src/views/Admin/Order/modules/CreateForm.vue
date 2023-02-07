<template>
  <a-modal
    :title="alertTitle"
    :width="640"
    :visible="visible"
    :confirmLoading="loading"
    @ok="
      () => {
        $emit('ok')
      }
    "
    @cancel="
      () => {
        $emit('cancel')
      }
    "
  >
    <a-spin :spinning="loading">
      <a-form :form="form" v-bind="formLayout">
        <a-alert :message="alertMessage" type="info" close-text="关闭" />
        <br />
        <!-- 检查是否有 id 并且大于0，大于0是修改。其他是新增，新增不显示主键ID -->
        <a-form-item v-show="model && model.id > 0" label="ID">
          <a-input v-decorator="['id', { initialValue: 0 }]" disabled />
        </a-form-item>
        <a-form-item label="用户角色">
          <a-select
            placeholder="请选择"
            v-decorator="['userRole', { rules: [{ required: true, message: '请选择用户角色' }] }]"
          >
            <a-select-option value="0">管理员</a-select-option>
            <a-select-option value="1">报修用户</a-select-option>
            <a-select-option value="2">维修工程师</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="姓名">
          <a-input v-decorator="['userName', { rules: [{ required: true, min: 2, message: '请输入用户姓名' }] }]" />
        </a-form-item>
        <a-form-item label="电话">
          <a-input
            v-decorator="['userPhone', { rules: [{ required: true, min: 10, message: '请输入正确的手机号' }] }]"
          />
        </a-form-item>
        <a-form-item label="工号" v-if="inputDisable">
          <a-input v-decorator="['userNo', { rules: [{ required: true, min: 5, message: '请输入学工号码' }] }]" />
        </a-form-item>
        <!-- <a-form-item label="密码">
          <a-input
            v-decorator="[
              'description',
              { rules: [{ required: true, min: 6, message: '请输入至少五个字符的规则描述！' }] }
            ]"
          />
        </a-form-item> -->
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['userRole', 'userName', 'userPhone', 'userNo', 'id']

export default {
  name: 'AlertForm',
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    loading: {
      type: Boolean,
      default: () => false
    },
    model: {
      type: Object,
      default: () => null
    },
    inputDisable: {
      type: Boolean,
      default: () => true
    },
    alertTitle: {
      type: String,
      default: () => '新建用户'
    },
    alertMessage: {
      type: String,
      default: () => '新增/修改用户信息后，密码默认重置为Abc@工号'
    }
  },
  data() {
    this.formLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      }
    }
    return {
      // formData: {},
      form: this.$form.createForm(this)
    }
  },
  created() {
    console.log('custom modal created')

    // 防止表单未注册
    fields.forEach((v) => this.form.getFieldDecorator(v))

    // 当 model 发生改变时，为表单设置值
    this.$watch('model', () => {
      console.log('watch', this.model)
      this.model && this.form.setFieldsValue(pick(this.model, fields))
    })
  },
  watch: {
    model: function () {
      console.log('watch', this.model)
      this.model && this.form.setFieldsValue(pick(this.model, fields))
    }
  },
  computed: {
    formData() {
      return this.form.getFieldsValue()
    }
  }
}
</script>
