<template>
  <div class="th-item">
    <div class="th-item-left">
      <!-- <img src="http://wawa-1255600302.file.myqcloud.com/images/lihe.png" alt=""> -->
      <img :src="img + item.previewImage" alt="">
      <div class="item-text">
        <p class="title">{{item.name}}— <span class="price">￥{{item.price | price}}</span></p>
        <p class="coin">买{{item.coinNum}}送{{item.giftCoinNum}}金币<span class="bag" @click="show">礼包说明</span></p>
        <div class="type">
          <i class="iconfont icon-icon-test"></i>
          <span class="time" v-if="item.timeLimitType === 1">永久 (限{{item.buyLimit}}次)</span>
          <span class="time" v-if="item.timeLimitType === 2">有效期：1月31日 10:00 至 1月31日 10:00 (限{{item.buyLimit}}次)</span>
          <span class="time" v-if="item.timeLimitType === 3">每天 (限{{item.buyLimit}}次)</span>
          <span class="time" v-if="item.timeLimitType === 4">每周 (限{{item.buyLimit}}次)</span>
          <span class="time" v-if="item.timeLimitType === 5">每月 (限{{item.buyLimit}}次)</span>
          
        </div>
      </div>
    </div>
    <div class="th-item-right">
      <div class="img-btn-right" @click="ordersBags">
        <img src="http://wawa-1255600302.file.myqcloud.com/images/pay2.png" alt="">
        <span class="img-text">购买</span>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      item: Object,
    },
    methods: {
      ordersBags () {
        this.$emit('ordersBags', this.item.id)
      },
      show () {
        this.$emit('show', this.item.description)
      }
    },
    filters: {
      price (val) {
        return (val / 100).toFixed(2)
      }
    }
  }
</script>

<style lang="less">
  .th-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1.5rem 0;
    position: relative;
    &:not(:last-child) {
      &:after {
        content: "";
        width: 100%;
        height: 1px;
        background: #fbe2e8;
        position: absolute;
        bottom: 0;
        transform: scaleY(1);
      }
      // border-bottom: 1px solid #fff;
    }
    .th-item-left {
      display: flex;
      img {
        width: 5rem;
        height: 5rem;
        flex-shrink: 0;
        margin-right: 1.5rem;
        border-radius: 5px;
      }
      .item-text {
        .title {
          color: #333;
          font-size: 1.6rem;
          margin-bottom: 5px;
          .price {
            color: #ffffff;
            background: #f6bcc8;
            padding: 0 .5rem;
            margin-right: .3rem;
          }
        }
        .coin {
          font-size: 1.4rem;
          color: #666666;
          .bag {
            color: #d72571;
            text-decoration: underline;
            margin-left: 1rem;
            font-size: 1.3rem;
          }
        } 
        .type {
          margin-top: .8rem;
          font-size: 1.2rem;
          color: #b0b0b0;
          .icon-icon-test {
            font-size: 1.3rem;
          }
        }
      }
    }
    .th-item-right {
      .img-btn-right {
        width: 6rem;
        line-height: 0;
        position: relative;
        img {
          width: 100%;
        }
        .img-text {
          position: absolute;
          font-size: 1.6rem;
          color: #fff;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          width: 100%;
          text-align: center;
          font-weight: bold;
        }
      }
    }
  }
</style>

