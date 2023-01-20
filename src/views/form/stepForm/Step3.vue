<template>
  <div>
    <a-form>
      <a-result
        title="操作成功"
        :is-success="true"
        sub-title="正在派单"
        style="max-width: 560px; margin: 40px auto 0"
      >
        <div class="information">
          <a-row>
            <a-col :sm="8" :xs="24">工单发起：</a-col>
            <a-col :sm="16" :xs="24">ant-design@alipay.com</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">工单接收：</a-col>
            <a-col :sm="16" :xs="24">test@example.com</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">工程师姓名：</a-col>
            <a-col :sm="16" :xs="24">辉夜</a-col>
          </a-row>
          <a-row>
            <a-col :sm="8" :xs="24">预计抵达时间：</a-col>
            <a-col :sm="16" :xs="24"><span class="money">500</span> 元</a-col>
          </a-row>
        </div>
        <template #extra>
          <a-button type="primary" @click="finish">再报一单</a-button>
          <a-button style="margin-left: 8px" @click="toOrderList">查看详情</a-button>
        </template>
      </a-result>
    </a-form>
  </div>
</template>

<script>
export default {
  name: 'Step3',
  data() {
    return {
      loading: false
    }
  },
  methods: {
    finish() {
      this.$emit('finish')
    },
    toOrderList() {
      this.$router.push({
        name: 'ProfileAdvanced',
        params: {
          keyId: this.$store.state.keyId
        }
      })
    }
  }
}
</script>
<style lang="less" scoped>
.information {
  line-height: 22px;

  .ant-row:not(:last-child) {
    margin-bottom: 24px;
  }
}
.money {
  font-family: 'Helvetica Neue', sans-serif;
  font-weight: 500;
  font-size: 20px;
  line-height: 14px;
}
</style>
