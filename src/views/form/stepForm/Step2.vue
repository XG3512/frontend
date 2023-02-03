<template>
  <div>
    <a-form :form="form" style="max-width: 500px; margin: 40px auto 0">
      <a-alert :closable="true" message="确认后，工单将进行派发，无法直接退回。" style="margin-bottom: 24px" />

      <a-form-item label="报修人电话" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        13020535093
      </a-form-item>
      <a-form-item label="报修人姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        Alex
      </a-form-item>
      <a-form-item label="报修地址" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        {{ $store.state.formStepFormAdress }}
      </a-form-item>
      <a-form-item label="问题描述" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        {{ $store.state.formStepFormDescription }}
      </a-form-item>
      <a-form-item label="期望维修时间" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        {{ $store.state.formStepTimeItems }}
      </a-form-item>
      <a-form-item label="" :labelCol="labelCol" :wrapperCol="wrapperCol" class="stepFormText">
        <a-divider />
        <div>
          <span style="display: block; text-align: center">手机扫码上传或点击图片二维码直接上传附件</span>
          <a :href="codeUrl" target="_Blank" style="display: block; margin: 0 auto">
            <vue-qr
              style="width: 80%; display: block; margin: 0 auto"
              :correctLevel="3"
              :autoColor="false"
              colorDark="#000000"
              :text="codeUrl"
              :size="95"
              :margin="0"
            ></vue-qr>
          </a>
        </div>
      </a-form-item>
      <a-form-item :wrapperCol="{ span: 19, offset: 5 }">
        <a-button :loading="loading" type="primary" @click="nextStep">提交</a-button>
        <a-button style="margin-left: 8px" @click="prevStep">上一步</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script>
import VueQr from 'vue-qr'
import { v4 } from 'uuid'
export default {
  name: 'Step2',
  data() {
    return {
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this),
      loading: false,
      timer: 0,
      keyId: '',
      codeUrl: ''
    }
  },
  created() {
    this.keyId = v4()
    // console.log(this.keyId)
    this.codeUrl = 'http://124.221.110.184/upload.php?keyId=' + this.keyId
    this.$store.state.formStepFormImage = this.keyId
    // console.log(this.codeUrl)
  },
  methods: {
    nextStep() {
      const that = this
      this.$store.state.keyId = that.keyId
      const {
        form: { validateFields }
      } = this
      that.loading = true
      validateFields((err, values) => {
        if (!err) {
          console.log('表单 values', values)
          that.timer = setTimeout(function () {
            that.loading = false
            that.$emit('nextStep')
          }, 500)
        } else {
          that.loading = false
        }
      })
    },
    prevStep() {
      this.$emit('prevStep')
    }
  },
  beforeDestroy() {
    clearTimeout(this.timer)
  },
  components: {
    VueQr
  }
}
</script>

<style lang="less" scoped>
.stepFormText {
  margin-bottom: 24px;

  .ant-form-item-label,
  .ant-form-item-control {
    line-height: 22px;
  }
}
</style>
