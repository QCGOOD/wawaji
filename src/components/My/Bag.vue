<template>
  <div class="bagdia" v-if="show">
    <div class="bag-box" :style="{width: `${width}px`}">
      <i class="iconfont icon-guanbi1" @click="close"></i>
      <div class="lbsm">
        <img src="http://wawa-1255600302.file.myqcloud.com/images/lbsm.png" alt="">
      </div>
      <div class="bag-text" v-html="description">
        <!-- <p v-for="i in 30" style="font-size: 18px;">fdsaf</p> -->
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      visibile: Boolean,
      description: String,
    },
    data () {
      return {
        width: 0,
      }
    },
    computed: {
      show () {
        return this.visibile
      }
    },
    mounted () {
      this.width = window.innerWidth - 30
      window.onresize = () => {
        this.width = window.innerWidth - 30
      }
    },
    methods: {
      close () {
        this.$emit('close')
      }
    }
  }
</script>

<style lang="less">
  .bagdia {
    background: rgba(0, 0, 0, .8);
    position: absolute;
    top: 0;
    z-index: 1;
    bottom: 0;
    left: 0;
    right: 0;
    .bag-box {
      background: #ffffff;
      position: absolute;
      border-radius: 15px;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 60%;
      // height: 70%;
      .icon-guanbi1 {
        position: absolute;
        top: -1px;
        right: 4px;
        font-size: 28px;
        color: #ffa8c3;
      }
      .lbsm {
        width: 35%;
        margin: 30px auto 0 auto;
        img {
          width: 100%;
        }
      }
      .bag-text {
        overflow: scroll;
        height: 50rem;
        font-size: 1.6rem;
        // color: #fff;
        padding: 1rem;
        box-sizing: border-box;
      }
    }
  }
</style>
