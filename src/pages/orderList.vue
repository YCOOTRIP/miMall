<template>
  <div class="order-list">
    <order-header title="订单列表">
      <template #tip>
        <span>请谨防钓鱼链接或诈骗电话，了解更多></span>
      </template>
    </order-header>
    <div class="wrapper">
      <div class="container">
        <div class="order-box">
          <loading v-if="loading"></loading>
          <div class="order" v-for="(order,index) in list" :key="index">
            <div class="order-title">
              <div class="item-info fl">
                {{order.createTime}}
                <span>|</span>
                {{order.receiverName}}
                <span>|</span>
                订单号：{{order.orderNo}}
                <span>|</span>
                {{order.paymentTypeDesc}}
              </div>
              <div class="item-money fr">
                <span>应付金额：</span>
                <span class="money">{{order.payment}}</span>
                <span>元</span>
              </div>
            </div>
            <div class="order-content clearfix">
              <div class="good-box fl">
                <div class="good-list" v-for="(item,i) in order.orderItemVoList" :key="i">
                  <div class="good-img">
                    <img v-lazy="item.productImage" alt="">
                  </div>
                  <div class="good-name">
                    <div class="p-name">{{item.productName}}</div>
                    <div class="p-money">{{item.totalPrice + 'X' + item.quantity}}元</div>
                  </div>
                </div>
              </div>
              <div class="good-state fr" v-if="order.status == 20">
                <a href="javascript:;">{{order.statusDesc}}</a>
              </div>
              <div class="good-state fr" v-else>
                <a href="javascript:;" @click="goPay(order.orderNo)">{{order.statusDesc}}</a>
              </div>
            </div>
          </div>
          <nodata v-if="!loading && list.length === 0"></nodata>
          <el-pagination v-if="list.length !== 0" class="pagination" background layout="prev, pager, next"
            :pageSize='pageSize' :total="total" @current-change="handleChange">
          </el-pagination>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import OrderHeader from '@/components/OrderHeader'
import Loading from '@/components/Loading'
import Nodata from '@/components/Nodata'
export default {
  name: 'orderList',
  components: {
    OrderHeader,
    Loading,
    Nodata
  },
  data () {
    return {
      list: [],
      loading: true,
      pageSize: 10,
      total: 0
    }
  },
  mounted () {
    this.getOrderList()
  },
  methods: {
    getOrderList () {
      this.axios.get('/orders', {
        params: {
          pageNum: this.pageNum
        }
      }).then(res => {
        this.loading = false
        this.list = res.list
        this.total = res.total
      }).catch(() => {
        this.loading = false
      })
    },
    goPay (orderNo) {
      this.$router.push({
        path: '/order/pay',
        query: {
          orderNo
        }
      })
    },
    handleChange (pageNum) {
      this.pageNum = pageNum
      this.getOrderList()
    }
  }
}
</script>

<style lang="scss">
@import '@/assets/scss/mixin.scss';
@import '@/assets/scss/config.scss';
.order-list {
  .wrapper {
    background-color: $colorJ;
    padding-top: 33px;
    padding-bottom: 40px;
    .order-box {
      .order {
        @include border();
        background-color: $colorG;
        margin-bottom: 31px;
        &:last-child {
          margin-bottom: 0;
        }
        .order-title {
          @include height(74px);
          background-color: $colorK;
          padding: 0 43px;
          font-size: 16px;
          color: $colorC;
          .item-info {
            span {
              margin: 0 9px;
            }
          }
          .money {
            font-size: 26px;
            color: $colorB;
          }
        }
        .order-content {
          padding: 0 43px;
          .good-box {
            width: 88%;
            .good-list {
              display: flex;
              align-items: center;
              height: 145px;
              .good-img {
                width: 112px;
                img {
                  width: 100%;
                }
              }
              .good-name {
                font-size: 20px;
                color: $colorB;
              }
            }
          }
          .good-state {
            @include height(145px);
            font-size: 20px;
            color: $colorA;
            a {
              color: $colorA;
            }
          }
        }
      }
      // .pagination{
      //   text-align:right;
      // }
      // .el-pagination.is-background .el-pager li:not(.disabled).active{
      //   background-color: #FF6600;
      // }
      // .el-button--primary{
      //   background-color: #FF6600;
      //   border-color: #FF6600;
      // }
      // .load-more,.scroll-more{
      //   text-align:center;
      // }
    }
    .pagination {
      text-align: right;
    }
    // 改变el-pagination 的默认样式
    .el-pagination.is-background .el-pager li:not(.disabled).active {
      background-color: #ff6600;
      color: #fff;
    }
    .el-pagination.is-background .el-pager li:not(.disabled):hover {
      color: unset;
    }
  }
}
</style>
