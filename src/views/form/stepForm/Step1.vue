<template>
  <div>
    <a-form :form="form" style="max-width: 500px; margin: 40px auto 0">
      <!-- <a-form-item
        label="付款账户"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
        <a-select
          placeholder="ant-design@alipay.com"
          v-decorator="['paymentUser', { rules: [{required: true, message: '付款账户必须填写'}] }]">
          <a-select-option value="1">ant-design@alipay.com</a-select-option>
        </a-select>
      </a-form-item> -->
      <a-form-item label="维修地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-input-group style="display: inline-block; vertical-align: middle" :compact="true">
          <a-select style="width: 100px" v-model="formAddressNumber">
            <a-select-option value="A楼">A楼</a-select-option>
            <a-select-option value="B楼">B楼</a-select-option>
          </a-select>
          <a-input
            :style="{ width: 'calc(100% - 100px)' }"
            placeholder="A101办公室"
            v-decorator="[
              'formAddressFull',
              { rules: [{ required: true, message: '详细地址必须填写' }] }
            ]"
          />
        </a-input-group>
      </a-form-item>
      <a-form-item label="期望维修时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-select
          mode="multiple"
          placeholder="请选择期望时间"
          style="width: 100%"
          @change="handleChange"
          v-decorator="['formStepTimeItems', { rules: [{ required: true, message: '期望维修时间必须填写' }] }]"
        >
          <a-select-option v-for="item in filteredOptions" :key="item" :value="item">
            {{ item }}
          </a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="问题描述" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-textarea
          placeholder="请仔细描述您遇到的问题"
          :rows="4"
          v-decorator="['formStepFormDescription', { rules: [{ required: true, messae: '请输入问题描述' }] }]"
        />
      </a-form-item>
      <a-form-item :wrapperCol="{ span: 19, offset: 5 }">
        <a-button type="primary" @click="nextStep">下一步</a-button>
      </a-form-item>
    </a-form>
    <!-- <a-divider /> -->
    <!-- <div class="step-form-style-desc">
      <h3>说明</h3>
      <h4>请尽量详细描述问题</h4>
      <p>
        如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。
      </p>
      <h4>请尽量详细描述问题</h4>
      <p>
        如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。如果需要，这里可以放一些关于产品的常见问题说明。
      </p>
    </div> -->
  </div>
</template>

<script>
const OPTIONS = ['01-12 13:00', '01-12 14:00', '01-13 10:30', '02-14 8:30']
export default {
  name: 'Step1',
  data() {
    return {
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this),
      selectedItems: [],
      formAddressNumber: 'A楼',
      formAddressFull: '',
      formDescription: '888'
    }
  },
  computed: {
    filteredOptions() {
      return OPTIONS.filter((o) => !this.selectedItems.includes(o))
    },
    formAddress() {
      // console.log(this.formAddressNumber + this.formAddressFull)
      return this.formAddressNumber + this.form.getFieldValue('formAddressFull')
    }
  },
  methods: {
    nextStep() {
      const that = this
      this.$store.state.formStepFormDescription = that.form.getFieldValue('formStepFormDescription')
      this.$store.state.formStepFormAdress = that.formAddress
      this.$store.state.formStepTimeItems = that.form.getFieldValue('formStepTimeItems')
      this.$store.state.formStepFormDescription = that.form.getFieldValue('formStepFormDescription')
      // console.log(this.$store.state.formStepTimeItems)
      const {
        form: { validateFields }
      } = this
      // 先校验，通过表单校验后，才进入下一步
      validateFields((err, values) => {
        if (!err) {
          this.$emit('nextStep')
        }
      })
    },
    handleChange(selectedItems) {
      this.selectedItems = selectedItems
    }
  }
}
</script>

<style lang="less" scoped>
.step-form-style-desc {
  padding: 0 56px;
  color: rgba(0, 0, 0, 0.45);

  h3 {
    margin: 0 0 12px;
    color: rgba(0, 0, 0, 0.45);
    font-size: 16px;
    line-height: 32px;
  }

  h4 {
    margin: 0 0 4px;
    color: rgba(0, 0, 0, 0.45);
    font-size: 14px;
    line-height: 22px;
  }

  p {
    margin-top: 0;
    margin-bottom: 12px;
    line-height: 22px;
  }
}
</style>
