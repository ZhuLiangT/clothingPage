<template>
  <div class="seasoning-condiments-html">
    <div class="top-content">
      <tool-bar>
        <Input v-model="goodsNumber" placeholder="请输入货号或简称"></Input>
        <Button class="search-button" icon="ios-search" type="primary">搜索</Button>
      </tool-bar>
      <div class="goods-show">
        <div class="item" v-for="goods in goodsData">
          <div class="goods-img" @click="imageShow=true">
            <img :src="imgUrl" class="html-cursor" alt="">
          </div>
          <div class="goods-introduction">
            <div class="code">货号:4544 / X1254</div>
            <div class="name">简称:5456</div>
            <div class="color">颜色:5456</div>
            <div class="size">尺码:5456</div>
            <div class="counts">数量:5456</div>
            <div class="operation-btn">
              <Button type="primary" size="small" @click="getGoodsShopRecord(goods.productId)">库存调拨</Button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <footer>
      <Page :total=100 :page-size=10 class="footer-page" @on-change=""></Page>
    </footer>
    <my-drawer :open="seasoningOpen" title="门店调货" @close-drawer="seasoningOpen=false"
               @complate-drawer="">
      <div class="seasoning-condiments">
        <div class="ui segment">
          <div class="ui vertical segment html-cursor" :class="{'active':repertory == 0}" @click="repertory = 0">
            <p>南京一号仓库 <span class="red">[1件库存]</span>
              <Icon v-if="repertory == 0" type="checkmark" color="#06b9a5" class="sure-icon"></Icon>
            </p>
          </div>
          <div class="ui vertical segment html-cursor" :class="{'active':repertory == 1}" @click="repertory = 1">
            <p>南京二号仓库 <span class="red">[5件库存]</span>
              <Icon v-if="repertory == 1" type="checkmark" color="#06b9a5" class="sure-icon"></Icon>
            </p>
          </div>
          <div class="ui vertical segment html-cursor" :class="{'active':repertory == 2}" @click="repertory = 2">
            <p>南京三号仓库 <span class="green">[10件库存]</span>
              <Icon v-if="repertory == 2" type="checkmark" color="#06b9a5" class="sure-icon"></Icon>
            </p>
          </div>
          <div class="ui vertical segment">
            <Input v-model="amount" placeholder="请输入数量"></Input>
          </div>
        </div>
      </div>
    </my-drawer>
    <Modal
      v-model="imageShow"
      title="图片展示">
      <img :src="imgUrl" alt="">
      <div slot="footer">
      </div>
    </Modal>
  </div>
</template>
<script>
  import myDrawer from '../../common/vue/myDrawer.vue';
  import toolBar from '../../common/vue/toolBar.vue';
  import seasoningConApi from '../../api/seasoningCondiments';

  export default {
    props: {},
    data() {
      return {
        account: this.$store.getters.getAccountId,
        shopId: this.$store.getters.getShopId,
        amount: '',
        goodsNumber: '',
        imageShow: false,
        imgUrl: 'https://img.alicdn.com/bao/uploaded/i2/1811379809/TB1qpkvlh3IL1JjSZPfXXcrUVXa_!!0-item_pic.jpg_430x430q90.jpg',
        repertory: 0,
        seasoningOpen: false,
        seasoningIn: '1',
        seasoningOut: '2',
        productShop: [],
        goodsData: [],
      };
    },
    created() {
      this.getAllSku();
    },
    methods: {
      postSeasoning() {
        let params = {
          fromShopId: '',
          toShopId: this.shopId,
          skuId: '',
          amount: this.amount
        };
        seasoningConApi.postSeasoning(this.account, params).then((rep) => {

        }).catch((rep) => {
          this.$error(apiError, '发起调货失败！');
        });
      },
      getGoodsShopRecord(productId) {
        this.seasoningOpen = true;
        seasoningConApi.getGoodsShopRecord(this.$store.getters.getAccountId, productId).then((rep) => {
          console.log(rep);
        }).catch((error) => {
          this.$error(apiError, '获取商品详细失败！');
        });
      },
      getAllSku() {
        let params = {
          account: this.$store.getters.getAccountId,
          shopId: this.$store.getters.getShopId,
          code: ''
        };
        seasoningConApi.getAllSku(params).then((rep) => {
          this.goodsData = rep.data;
        }).catch((rep) => {
          this.$error(apiError, '获取调货记录失败！')
        });
      }
    },
    components: {
      myDrawer,
      toolBar
    }
  };
</script>
<style lang="scss" rel="stylesheet/scss" type="text/scss">
  .seasoning-condiments-html {
    height: 100%;
    .top-content {
      height: 94%;
      overflow-y: auto;
    }
    .goods-show {
      margin-left: -2%;
      .item {
        width: 100%;
        padding: 15px;
        height: 80px;
        font-size: 14px;
        margin: {
          top: 8px;
          left: 2%;
        }
        background-color: #f8f6f2;
        display: flex;
        border: 1px solid rgba(34, 36, 38, .15);
        &:hover {
          box-shadow: 0 2px 4px 0 rgba(34, 36, 38, .12), 0 2px 10px 0 rgba(34, 36, 38, .15);
        }
        .goods-img {
          img {
            width: 50px;
            height: 50px;
          }
        }
        .goods-introduction {
          margin-left: 14px;
          flex: 1;
          display: flex;
          line-height: 50px;
          .code, .name, .color, .size, .counts {
            flex: 1;
          }
          .operation-btn {
            width: 120px;
            font-size: 14px;
          }
        }
      }
    }
    footer {
      margin-top: 8px;
      .footer-page {
        text-align: right;
      }
    }
  }

  .seasoning-condiments {
    padding: 8px;
    .red {
      color: red;
    }
    .green {
      color: #06b9a5;
    }
    .ui.segment {
      background: #fff;
      box-shadow: 0 1px 2px 0 rgba(34, 36, 38, .15);
      margin: 1rem 0;
      padding: 1em 1em;
      border-radius: .28571429rem;
      border: 1px solid rgba(34, 36, 38, .15);
    }

    .ui.vertical.segment {
      margin: 0;
      padding-left: 8px;
      padding-right: 0;
      background: none transparent;
      border-radius: 0;
      box-shadow: none;
      border: none;
      border-bottom: 1px solid rgba(34, 36, 38, .15);
      &.active {
        background-color: #f8f6f2;
      }
    }

    .ivu-select-dropdown {
      position: absolute !important;
    }
    .sure-icon {
      margin-right: 8px;
      float: right;
    }

  }
</style>
