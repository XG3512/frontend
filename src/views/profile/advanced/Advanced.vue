<template>
  <page-header-wrapper :title="keyIdNum" :tab-active-key="tabActiveKey" @tabChange="handleTabChange">
    <template v-slot:content>
      <a-descriptions size="small" :column="isMobile ? 1 : 2">
        <a-descriptions-item label="创建人">{{ order.creatName }}</a-descriptions-item>
        <a-descriptions-item label="工单地址">{{ order.address }}</a-descriptions-item>
        <a-descriptions-item label="创建时间">{{ order.creatTime }}</a-descriptions-item>
        <!-- <a-descriptions-item label="关联单据">
          <a href="">12421</a>
        </a-descriptions-item> -->
        <!-- <a-descriptions-item label="生效日期">2017-07-07 ~ 2017-08-08</a-descriptions-item> -->
        <a-descriptions-item label="问题描述">{{ order.description }}</a-descriptions-item>
      </a-descriptions>
    </template>

    <!-- actions -->
    <template v-slot:extra>
      <a-button type="primary" @click="showModal">取消工单</a-button>
      <a-modal v-model="visibleCancle" title="取消工单" @ok="cancelOrder(keyId)">
        <p>确定取消该工单？</p>
      </a-modal>
    </template>

    <template v-slot:extraContent>
      <a-row class="status-list">
        <a-col :xs="12" :sm="12">
          <div class="text">状态</div>
          <div class="heading">{{ statusMessage }}</div>
        </a-col>
        <!-- <a-col :xs="12" :sm="12">
          <div class="text">订单金额</div>
          <div class="heading">¥ 568.08</div>
        </a-col> -->
      </a-row>
    </template>

    <a-card :bordered="false" title="流程进度">
      <a-steps :direction="(isMobile && 'vertical') || 'horizontal'" :current="order.status" progressDot>
        <a-step>
          <template v-slot:title>
            <span>创建工单</span>
          </template>
          <template v-slot:description>
            <div class="antd-pro-pages-profile-advanced-style-stepDescription">
              {{ order.creatName }}
              <div>{{ order.creatTime }}</div>
            </div>
          </template>
        </a-step>
        <!-- <a-step>
          <template v-slot:title>
            <span>派发工单</span>
          </template>
          <template v-slot:description>
            <div class="antd-pro-pages-profile-advanced-style-stepDescription">
              <div v-show="true"><a>催一下</a></div>
              {{ distributeMessage }}
            </div>
          </template>
        </a-step> -->
        <a-step>
          <template v-slot:title>
            <span>工单处理</span>
          </template>
          <template v-slot:description>
            <div class="antd-pro-pages-profile-advanced-style-stepDescription">
              {{ order.fixName }}
              <div>{{ order.fixTime }}</div>
            </div>
          </template>
        </a-step>
        <a-step>
          <template v-slot:title>
            <span>服务评价</span>
          </template>
          <template v-slot:description v-if="order.achieveScore !== 0">
            <div class="antd-pro-pages-profile-advanced-style-stepDescription">
              <a-rate :value="order.achieveScore" disabled />
              <div>{{ order.fixTime }}</div>
            </div>
          </template>
        </a-step>
        <a-step title="完成" />
      </a-steps>
    </a-card>

    <a-card v-if="order.status === 1" style="margin-top: 24px" :bordered="false" title="工单处理">
      <a-descriptions>
        <a-descriptions-item label="工程师姓名">{{ order.fixName }}</a-descriptions-item>
        <a-descriptions-item label="工程师服务ID">{{ order.fixerId }}</a-descriptions-item>
        <a-descriptions-item label="邮箱">{{ order.fixerEmail }}</a-descriptions-item>
        <a-descriptions-item label="电话">{{ order.fixerPhone }}</a-descriptions-item>
        <a-descriptions-item label="QQ">{{ order.fixerQQ }}</a-descriptions-item>
        <a-descriptions-item label="派单状态">{{ order.fixStatus }}</a-descriptions-item>
      </a-descriptions>
    </a-card>
    <a-card v-if="order.status === 2" style="margin-top: 24px" :bordered="false" title="服务评价">
      <br />
      <div>
        <a-row type="flex" justify="space-around" align="middle">
          <a-col :span="8">
            <a-alert v-if="visible" message="评价已提交" type="success" closable :after-close="handleClose" />
            <br />
            <a-rate
              v-model="order.achieveScore"
              :allow-clear="false"
              :disabled="starStatu"
              @change="change(order.achieveScore)"
              style="justify-content: center; display: flex"
            >
              <a-icon slot="character" type="star" theme="filled" style="font-size: 59px" />
            </a-rate>
          </a-col>
        </a-row>
        <br />
        <br />
      </div>
    </a-card>
    <a-card v-if="order.status === 3" style="margin-top: 24px" :bordered="false" title="完成信息">
      <a-descriptions>
        <a-descriptions-item label="工程师姓名">{{ order.fixName }}</a-descriptions-item>
        <a-descriptions-item label="工程师服务ID">{{ order.fixerId }}</a-descriptions-item>
        <a-descriptions-item label="工单状态">已完成</a-descriptions-item>
        <a-descriptions-item label="结单时间">{{ order.achieveTime }}</a-descriptions-item>
        <a-descriptions-item label="服务评价">{{ achieveStar[order.achieveScore - 1] }}</a-descriptions-item>
        <a-descriptions-item label="派单状态">{{ order.fixStatus }}</a-descriptions-item>
      </a-descriptions>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import { baseMixin } from '@/store/app-mixin'
import { getOrderDeatl } from '@/api/manage'

export default {
  name: 'Advanced',
  mixins: [baseMixin],
  data() {
    return {
      keyId: '',
      // tabList: [{ key: 'detail', tab: '详情' }],
      postData: {},
      tabActiveKey: 'detail',
      distributeMessage: '2023-01-11',
      distributeTime: '2023-01-11',
      operationActiveTabKey: '1',
      // order: {
      //   status: 2,
      //   address: 'A 楼 A101办公室',
      //   description: '电脑黑屏',
      //   creatName: 'Alex',
      //   creatTime: '2023-01-12 12:32',
      //   fixName: 'xcc',
      //   fixerId: '20001032',
      //   fixTime: '2023-01-12 12:32',
      //   fixerEmail: 'xg3512@gmail.com',
      //   fixerPhone: '13020535093',
      //   fixerQQ: '2412650616',
      //   fixStatus: '工程师已确认', // 是否接单确认
      //   achieveTime: '2023-01-12 14:32',
      //   achieveScore: 0
      // },
      order: {},
      starStatu: false,
      visible: false,
      visibleCancle: false,
      achieveStar: ['一般', '好', '很好', '非常好', '极好']
      // orderId: this.$route.params.id
    }
  },
  created() {
    this.keyId = this.$route.params.keyId
    this.postData.orderId = this.$route.params.keyId
    console.log('creat')
    this.getData(this.postData)
  },
  filters: {
    statusFilter(status) {
      const statusMap = {
        agree: '成功',
        reject: '驳回'
      }
      return statusMap[status]
    },
    statusTypeFilter(type) {
      const statusTypeMap = {
        agree: 'success',
        reject: 'error'
      }
      return statusTypeMap[type]
    }
  },
  methods: {
    handleTabChange(key) {
      console.log('')
      this.tabActiveKey = key
    },
    change(value) {
      this.starStatu = true
      this.visible = true
      this.sendValue(value)
      this.order.status += 1
    },
    handleClose() {
      this.visible = false
    },
    sendValue(value) {
      console.log(value)
    },
    cancelOrder(id) {
      console.log(id)
      this.visibleCancle = false
    },
    showModal() {
      this.visibleCancle = true
    },
    getData(postData) {
      console.log('postData', postData)
      getOrderDeatl(postData).then((res) => {
        // console.log('postData', postData)
        console.log('res', res.result.data)
        this.order = res.result.data
      })
    }
  },
  computed: {
    keyIdNum() {
      return '工单详情：' + this.keyId
    },
    statusMessage() {
      if (this.order.status === 0) return '创建工单'
      else if (this.order.status === 1) return '工单处理'
      else if (this.order.status === 2) return '服务评价'
      else if (this.order.status === 3) return '服务完成'
      else return 'Err'
    }
  }
}
</script>

<style lang="less" scoped>
.detail-layout {
  margin-left: 44px;
}
.text {
  color: rgba(0, 0, 0, 0.45);
}

.heading {
  color: rgba(0, 0, 0, 0.85);
  font-size: 20px;
}

.no-data {
  color: rgba(0, 0, 0, 0.25);
  text-align: center;
  line-height: 64px;
  font-size: 16px;

  i {
    font-size: 24px;
    margin-right: 16px;
    position: relative;
    top: 3px;
  }
}

.mobile {
  .detail-layout {
    margin-left: unset;
  }
  .text {
  }
  .status-list {
    text-align: left;
  }
}
</style>
