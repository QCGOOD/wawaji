<template>
  <div class="inviting" v-show="show">
    <div class="inviting-box">
      <div class="img" id="simg">
        <!-- <div class="sguanbi" @click="close">
          <i class="iconfont icon-guanbi1"></i>
        </div> -->
        <i class="transparent-close" @click="close"></i>
        <img src="http://wawa-1255600302.file.myqcloud.com/images/yaoqing2.png" alt="">
      </div>
      <div class="mask" id="maskBox">
        
        <div class="inviting-text" v-if="share.length > 0">
          <div class="title">
            <p class="you-btn" @click="showInvting">
              <i class="big-circle"></i>
              <i class="small-circle"></i>
              邀请好友
            </p>
          </div>
          <div class="context">
            <div class="item" v-for="(item, i) in share" :key="i">
              <div class="item-l">
                <div class="item-img">
                  <img :src="item.headImage" alt="">
                  <!-- <img src="http://wawa-1255600302.file.myqcloud.com/images/headImg.jpg" alt=""> -->
                </div>
                <div class="info">
                  <p>成功邀请到{{item.nickname}}</p>
                  <p>奖励：金币x{{item.coinNum}}</p>
                </div>
              </div>
              <div class="item-r">
                <!-- <p class="receive" v-if="item.status === 0" @click="showInvting">邀请</p> -->
                <div class="receive-n" v-if="item.status === 1" @click="receive(item)">
                  <i class="big-circle"></i>
                  <i class="small-circle"></i>
                  领取
                </div>
                <!-- <p class="receive have-receive" v-if="item.status === 1" @click="receive(item)">领取</p> -->
                <i class="iconfont icon-dui" v-else-if="item.status === 2"></i>
              </div>
            </div>
          </div>
          <div class="shouming">
            <p>*好友进入游戏后，抓取一次娃娃即算邀请成功;</p>
            <!-- <p>*每晚12点重置进度，别忘记领奖啊~</p> -->
          </div>
        </div>
        <div class="inviting-none" v-else>
          <div class="none-text">
            <p>各位玩友，邀请好友即可得金币哦（好友进入游戏后，抓取一次娃娃即算邀请成功），分享多多，金币多多哦~</p>
          </div>
          <p class="none-btn" @click="showInvting">
            <i class="big-circle"></i>
              <i class="small-circle"></i>
            我要分享
          </p>
        </div>
      </div>
      <div class="inviting-mask">

      </div>
    </div>
  </div>
</template>

<script>
  import api from '../../api/api'
  import { mapState, mapActions } from 'vuex'

  export default {
    props: {
      visibile: Boolean,
    },
    data () {
      return {
        share: [],
        // show: false,
        invitationNum: 0,
      }
    },
    computed: {
			...mapState({
				sum: function (state) {
					let localSum = window.localStorage.getItem('sum')
					if (state.sum === 0 && localSum) {
						this.$store.commit('updateSum', {sum: localSum})
					}
					return state.sum
				},
      }),
      show () {
        return this.visibile
      },
    },
    mounted () {
      // this.shareInvitationReward()
      // this.shareInvitationRewardSelect()
    },
    methods: {
      ...mapActions([
        'updateMember', 'updateSum'
      ]),
      close () {
        // this.show = false
        this.$emit('close', 1)
      },
      showInvting () {
        this.$emit('show', true)
      },
      shareInvitationReward () {
        api.shareInvitationReward().then(res => {
          if (res.data.errCode === 0) {
            console.log('邀请好友配置')
            console.log(res.data)
            let shareInvitation = res.data.result
            let index = 0
            for (var key in shareInvitation) {
              if (/invitation/.test(key)) {
                let a = key.slice(10, 11)
                if (index === 0) {
                  index = a
                } else {
                  if (index < a) index = a
                }
              }
            }
            let arr = []
            for (var i = 0; i < index+1; i++) {
              for (let key in shareInvitation) {
                if (/invitation/.test(key)) {
                  let str = 'invitation' + i + 'Coin'
                  if (str === key) {
                    arr.push({number: i, gold: shareInvitation[key], status: 0})
                  }
                }
              }
            }
            this.share = arr
            this.shareInvitationRewardSelect()
          } else {
            this.showInfo(res.data.errMsg)
          }
        })
      },
      shareInvitationRewardSelect () {
        api.shareInvitationRewardSelect().then(res => {
          if (res.data.errCode === 0) {
            console.log('shareInvitationRewardSelect')
            console.log(res.data)
            this.invitationNum = res.data.result.invitationNum
            // let result = {}
            // if (res.data.result.invitations) {
            //   result = res.data.result.invitations
            //   for (let key in result) {
            //     this.share.map(item => {
            //       if (item.number === Number(key)) {
            //         item.id = result[key].id
            //         item.status = result[key].status
            //       }
            //     })
            //   }
            //   console.log(this.share)
            // }
            this.share = res.data.result.invitations
          } else {
            this.showInfo(res.data.errMsg)
          }
        })
      },
      // 领取
      receive (item) {
        api.obtainInvitationCoin({id: item.id}).then(res => {
          console.log(res.data)
          if (res.data.errCode === 0) {
            this.updateSum(this.sum + item.coinNum)
            this.showInfo(`领取${item.coinNum}金币成功`)
            item.status = 2
          }
        })
      }
    }
  }
</script>


<style lang="less">
@import './../../assets/css/public.less';
  .inviting {
    background: rgba(0, 0, 0, .6);
    position: absolute;
    top: 0;
    z-index: 10;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    .inviting-box {
      width: 76%;
      position: relative;
      .img {
        .sguanbi {
          position: absolute;
          color: #fff;
          right: 10px;
          top: -18px;
          &:after {
            content: '';
            width: 2px;
            height: 30px;
            background: #fff;
            display: inline-block;
            position: absolute;
            left: 50%;
            top: 10px;
            transform: translate(-50%, 0);
            z-index: -1;
          }
          .icon-guanbi1 {
            font-size: 28px;
          }
        }
        width: 110%;
        position: relative;
        left: 50%;
        transform: translate(-50%, 0);
        line-height: 0;
        margin-bottom: -3.9rem;
        z-index: 1;
        .transparent-close {
          width: 3.5rem;
          height: 3.5rem;
          display: block;
          position: absolute;
          right: 0;
          border-radius: 50%;
          top: 50%;
          transform: translate(0, -50%);
        }
        img {
          width: 100%;
        }
      }
      .mask {
        background: rgba(255, 255, 255, .8);
        border-radius: 10px;
        padding: 1rem;
        box-sizing: border-box;
        .inviting-none {
          font-size: 1.6rem;
          background: #fff;
          height: 34rem;
          display: flex;
          justify-content: center;
          align-items: center;
          flex-flow: column;
          .none-text {
            padding: 0 4rem;
            font-size: 1.7rem;
          }
          .none-btn {
            margin-top: 3rem;
            // .bgLinearGradient(@top: #fe2d87; @bottom: #f22065;);
            // width: 14rem;
            // height: 4rem;
            // line-height: 4rem;
            // text-align: center;
            // color: #fff;
            // font-size: 1.8rem;
            // border-radius: 40px;
            // border: 1px solid #ff3e94;
            // .boxShadowOutset(@x: 0px; @y: 2px; @blur: 0px; @spread: 0px; @color: #790029;);
            .bgLinearGradient(@top: #ff4d99; @bottom: #f42168;);
            .borderRadius(10px);
            .boxShadowOutset(@x: 0; @y: .7rem; @blur: 0; @spread: 0; @color: #c10846;);
            width: 14rem;
            height: 5rem;
            line-height: 5rem;
            font-size: 2rem;
            text-align: center;
            color: #fff;
            position: relative;
            .big-circle {
              width: 1.3rem;
              height: 1.3rem;
              background: rgba(255, 255, 255, 0.5);
              position: absolute;
              left: .5rem;
              top: .6rem;
              border-radius: 50%;
            }
            .small-circle {
              width: .9rem;
              height: .9rem;
              background: rgba(255, 255, 255, 0.5);
              position: absolute;
              left: 2.2rem;
              top: .4rem;
              border-radius: 50%;
            }
          }
        }
        .inviting-text {
          text-align: center;
          background: #fff;
          // height: 100%;
          border-radius: 10px;
          position: relative;
          padding: 3rem 1.5rem 0 1.5rem;
          .title {
            // background: #f6a0b5;
            border-radius: 5px;
            color: #fff;
            margin-bottom: 2rem;
            padding: .5rem 1rem;
            display: flex;
            justify-content: center;
            align-items: center;
            .you-btn {
              // .bgLinearGradient(@top: #fe2d87; @bottom: #f22065;);
              // width: 14rem;
              // height: 4rem;
              // line-height: 4rem;
              // text-align: center;
              // color: #fff;
              // font-size: 1.8rem;
              // border-radius: 40px;
              // border: 1px solid #ff3e94;
              // .boxShadowOutset(@x: 0px; @y: 2px; @blur: 0px; @spread: 0px; @color: #790029;);
              .bgLinearGradient(@top: #ff4d99; @bottom: #f42168;);
              .borderRadius(10px);
              .boxShadowOutset(@x: 0; @y: .7rem; @blur: 0; @spread: 0; @color: #c10846;);
              width: 14rem;
              height: 5rem;
              line-height: 5rem;
              font-size: 2rem;
              text-align: center;
              color: #fff;
              position: relative;
              .big-circle {
                width: 1.3rem;
                height: 1.3rem;
                background: rgba(255, 255, 255, 0.5);
                position: absolute;
                left: .5rem;
                top: .6rem;
                border-radius: 50%;
              }
              .small-circle {
                width: .9rem;
                height: .9rem;
                background: rgba(255, 255, 255, 0.5);
                position: absolute;
                left: 2.2rem;
                top: .4rem;
                border-radius: 50%;
              }
            }
            .left {
              text-align: left;
              h3 {
                font-size: 1.6rem;
              }
              p {
                font-size: 1.3rem;
                color: #fff;
              }
            }
            .right {
              p {
                &:first-child {
                  font-size: 1.4rem;
                  .bgLinearGradient(@top: #fe2984; @bottom: #f32066;);
                  padding: .3rem .8rem;
                  .borderRadius(30px);
                  line-height: 1;
                  color: #fff; 
                  border: 2px solid #ff3e94;
                  .boxShadowOutset(@x: 0; @y: 3px; @blur: 0px; @spread: 0px; @color: #75001d);
                }
                &:last-child {
                  font-size: 1.2rem;
                  color: #fff5c4;
                  margin-top: 3px;
                }
              }
            }
          }
          .context {
            height: 25rem;
            overflow-y: scroll;
            top: 11rem;
            bottom: 2rem;
            left: 2rem;
            right: 2rem;
            padding-right: 1px;
            .item {
              display: flex;
              align-items: center;
              padding: .7rem;
              background: #ffeff3;
              border-radius: 5px;
              border: 1px solid #f7c5d1;
              box-sizing: border-box;
              justify-content: space-between;
              &:not(:first-child) {
                margin-top: 5px;
              }
              .item-l {
                display: inline-flex;
                align-items: center;
                .item-img {
                  width: 4rem;
                  height: 4rem;
                  line-height: 1;
                  overflow: hidden;
                  margin-right: 15px;
                  flex-shrink: 0;
                  img {
                    width: 100%;
                  }
                }
                .info {
                  text-align: left;
                  p {
                    line-height: 1;
                    &:first-child {
                      font-size: 1.5rem;
                      font-weight: bold;
                      color: #333333;
                      margin-bottom: 6px;
                    }
                    &:last-child {
                      font-size: 1.4rem;
                      color: #b0b0b0;
                    }
                  }
                }
              }
              .item-r {
                .r-count {
                  color: #b79e44;
                  font-size: 1.2rem;
                }
                .receive {
                  .bgLinearGradient(@top: #32cddf; @bottom: #0d90a6;);
                  .borderRadius(30px);
                  color: #fff;
                  font-size: 1.3rem;
                  
                  width: 6rem;  
                  height: 2.2rem;
                  line-height: 2.2rem;
                  text-align: center;
                  border: 1px solid #2cc4d7;
                  // box-sizing: border-box;
                  .boxShadowOutset(@x: 0; @y: 2px; @blur: 0px; @spread: 0px; @color: #006766);
                }
                .have-receive {
                  .bgLinearGradient(@top: #9be000; @bottom: #49a701;);
                  border: 1px solid #9eee00;
                  .boxShadowOutset(@x: 0; @y: 2px; @blur: 0px; @spread: 0px; @color: #417a00);
                }
                .receive-n {
                  .bgLinearGradient(@top: #9ce000; @bottom: #48a801;);
                  .borderRadius(.9rem);
                  .boxShadowOutset(@x: 0; @y: .4rem; @blur: 0; @spread: 0; @color: #478a0e;);
                  width: 6rem;
                  height: 3.2rem;
                  line-height: 3.4rem;
                  font-size: 1.5rem;
                  text-align: center;
                  color: #fff;
                  position: relative;
                  .big-circle {
                    width: .7rem;
                    height: .7rem;
                    background: rgba(255, 255, 255, 0.5);
                    position: absolute;
                    left: .4rem;
                    top: .5rem;
                    border-radius: 50%;
                  }
                  .small-circle {
                    width: .5rem;
                    height: .5rem;
                    background: rgba(255, 255, 255, 0.5);
                    position: absolute;
                    left: 1.4rem;
                    top: .3rem;
                    border-radius: 50%;
                  }
                }
                .icon-dui {
                  color: #60b700;
                  font-size: 2.6rem;
                  margin-top: -5px;
                  line-height: .8;
                }
              }
            }
          }
          .shouming {
            color: #999999;
            font-size: 1.4rem;
            padding: 1rem 0 1rem 0;
            text-align: left;
          }
        }
      }
    }
  }
</style>
