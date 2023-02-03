<template>
  <div>
    <a-card
      v-for="(item, index) in orderList"
      :title="item.orderType"
      :bordered="true"
      :key="index"
      style="margin-bottom: 20px"
    >
      <a-row type="flex" justify="center">
        <a-col :xs="24" :sm="24" :md="24" :lg="10" :xl="6">
          <template>
            <a-carousel autoplay>
              <div v-for="(imageUrl, index2) in item.orderImageList" :key="index2">
                <img :src="baseUrl + imageUrl" />
              </div>
            </a-carousel>
          </template>
        </a-col>
        <a-col :xs="24" :sm="24" :md="24" :lg="11" :xl="16">
          <template>
            <a-card
              :bordered="false"
              style="width: 90%; height: auto; margin: 0 auto"
              :tab-list="item.tabListNoTitle"
              :active-tab-key="item.noTitleKey"
              @tabChange="(key) => onTabChange(key, index)"
            >
              <div v-for="(p, index3) in item.tabListNoTitle" :key="index3">
                <p v-if="item.noTitleKey === p.key">{{ p.description }}</p>
              </div>
            </a-card>
          </template>
        </a-col>
        <a-col :xs="24" :sm="24" :md="24" :lg="3" :xl="2">
          <template>
            <a-statistic title="客户预算" :value="item.orderMoney" />
            <br />
            <a-statistic-countdown title="结单期限" :value="item.orderDay" format="D 天" />
            <br />
            <br />
            <br />
            <a-button type="primary" size="large" block style="min-width:50px "> 立即接单 </a-button>
          </template>
        </a-col>
      </a-row>
    </a-card>
  </div>
</template>
<style scoped>
/* For demo */
.ant-carousel >>> .slick-slide {
  text-align: center;
  height: 250px;
  line-height: 180px;
  /* background: #364d79; */
  overflow: hidden;
}

.ant-carousel >>> .slick-slide img {
  display: block;
  margin: auto;
  max-width: 100%;
}
.ant-carousel >>> .slick-dots li button {
  background-color: rgb(58, 58, 58);
}
</style>
<script>
// const baseUrl =
//   'https://raw.githubusercontent.com/vueComponent/ant-design-vue/master/components/vc-slick/assets/img/react-slick/abstract0'
export default {
  name: 'Error',
  data() {
    return {
      baseUrl:
        'https://raw.githubusercontent.com/vueComponent/ant-design-vue/master/components/vc-slick/assets/img/react-slick/abstract0',
      deadline: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30,
      orderList: [
        {
          orderId: '001',
          orderType: '网页开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1',
              description: '网页开发需求1'
            },
            {
              key: 'app',
              tab: '描述2',
              description: '网页开发需求2'
            },
            {
              key: 'project',
              tab: '描述3',
              description: '网页开发需求3'
            },
            {
              key: '4',
              tab: '描述4',
              description: '网页开发需求4'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '1000',
          orderDay: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30
        },
        {
          orderId: '002',
          orderType: '小程序开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1',
              description: '小程序订单需求1'
            },
            {
              key: 'app',
              tab: '描述2',
              description: '小程序订单需求2'
            },
            {
              key: 'project',
              tab: '描述3',
              description: '小程序订单需求3'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '500',
          orderDay: Date.now() + 500 * 60 * 60 * 24 * 2 + 1000 * 30
        },
        {
          orderId: '001',
          orderType: '网页开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1',
              description: '1'
            },
            {
              key: 'app',
              tab: '描述2',
              description: '2'
            },
            {
              key: 'project',
              tab: '描述3',
              description: '3'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '1000',
          orderDay: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30
        },
        {
          orderId: '001',
          orderType: '网页开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1',
              description: '1'
            },
            {
              key: 'app',
              tab: '描述2',
              description: '2'
            },
            {
              key: 'project',
              tab: '描述3',
              description: '3'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '1000',
          orderDay: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30
        },
        {
          orderId: '001',
          orderType: '网页开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1',
              description: '1'
            },
            {
              key: 'app',
              tab: '描述2',
              description: '2'
            },
            {
              key: 'project',
              tab: '描述3',
              description: '3'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '1000',
          orderDay: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30
        },
        {
          orderId: '002',
          orderType: '小程序开发单',
          orderImageList: ['1.jpg', '2.jpg', '3.jpg', '4.jpg'],
          tabListNoTitle: [
            {
              key: 'article',
              tab: '描述1'
            },
            {
              key: 'app',
              tab: '描述2'
            },
            {
              key: 'project',
              tab: '描述3'
            }
          ],
          noTitleKey: 'article',
          orderMoney: '1000',
          orderDay: Date.now() + 1000 * 60 * 60 * 24 * 2 + 1000 * 30
        }
      ]
    }
  },
  methods: {
    onTabChange(key, index) {
      const that = this
      that.orderList[index]['noTitleKey'] = key
      console.log(key, index, that.orderList[index]['noTitleKey'])
    }
  },
  computed: {
    result() {
      return {
        title: this.$t('result.fail.error.title'),
        description: this.$t('result.fail.error.description')
      }
    }
  }
}
</script>
