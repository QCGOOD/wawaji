<template>
	<div class="main" id="main" :class="{'hidden': isHidden}">
		<!-- 头部 -->
		<div class="header" id="header">
      <router-link tag="div" class="user-info" :to="{path: '/user'}">
        <img v-if="userInfo.headImage" :src="userInfo.headImage" alt="">
        <img v-else src="http://wawa-1255600302.file.myqcloud.com/images/headImg.jpg" alt="">
				<!-- <p class="vip" v-if="userInfo.charged">V1</p> -->
        <p>
          <span style="margin-left: 5px; color: #613414;">ID:{{userInfo.number}}</span>
          <span style="margin-left: 5px; color: #613414;">金币:{{sum}}</span>
        </p>
      </router-link>
      <div class="function">
				<!-- <router-link tag="i" class="img" :to="{path: '/myInfo/Preferential'}">
          <img class="lihe" src="http://wawa-1255600302.file.myqcloud.com/images/lihe.png" alt="">
        </router-link> -->
        <router-link tag="i" class="img" :to="{path: '/myInfo/recharge'}">
          <img src="http://wawa-1255600302.file.myqcloud.com/images/pay1.png" alt="">
        </router-link>
        <router-link tag="i" class="img" :to="{path: '/myInfo'}">
          <img src="http://wawa-1255600302.file.myqcloud.com/images/setting1.png" alt="">
        </router-link>
      </div>
    </div>

		
		<div class="main-box" id="main-box">
			<!-- 轮播图 -->
			<swipers moduleName="News" :type="false"></swipers>
			<!-- 按钮 -->
			<div class="function-button">
				<!-- <div class="button-item" v-for="(item, i) in functionButtons" :key="i" @click="show(item, i)">
					<img :src="`http://wawa-1255600302.file.myqcloud.com/images/${item.imgName}.png`" alt="">
					<span>{{item.title}}</span>
				</div> -->

				<div class="button-item" @click="show(0)">
					<img :src="`http://wawa-1255600302.file.myqcloud.com/images/btn_1.png`" alt="">
					<span>每日礼包</span>
				</div>
				<div class="button-item" @click="show(1)" v-if="false">
					<img :src="`http://wawa-1255600302.file.myqcloud.com/images/btn_2.png`" alt="">
					<span>邀请有礼</span>
				</div>
				<div class="button-item" @click="show(2)">
					<img :src="`http://wawa-1255600302.file.myqcloud.com/images/btn_3.png`" alt="">
					<span>新手福利</span>
				</div>
				<div class="button-item" @click="show(3)">
					<img :src="`http://wawa-1255600302.file.myqcloud.com/images/btn_4.png`" alt="">
					<span>游戏指引</span>
				</div>
			</div>
			<!-- 分类 -->
			<div class="tabs" id="tabs">
				<div class="tabs_wrap" id="tabs_wrap" :class="{'fixed': fixedTab}">
					<div class="tabs_nav">
						<div class="tabs_nav-bar" :style="{width: `${tabWidth}px`, transform: `translate3d(${translateWidth}px,0,0)`}"></div>
						<div class="tab-item" v-for="(item, index) in channelsTypeList" :key="index" :class="{'tab-active': tabActive === index}" @click="clickActive(index, item)">
							<span>{{item.name}}</span>
						</div>
					</div>
				</div>
			</div>
			<!-- 频道列表 -->
			<div class="listImg">
				<div class="graphic" v-for="(item, i) in newsList" :key="i" @click="to(item.id)">
					<graphic :item="item"></graphic>
				</div>
			</div>
			<divider v-if="shows" style="color: rgb(125, 113, 113); font-size: 1.4rem;">我是有底线的</divider>
			<p style="text-align:center; padding: 10px 0;" v-else>
				<inline-loading></inline-loading><span style="vertical-align:middle;display:inline-block;font-size:14px;">&nbsp;&nbsp;数据加载中</span>
			</p>
		</div>

		<login-bonus :visibile="isLoginBonus" @close="getMasks"></login-bonus>
		<daily-gift-bag :visibile="isDailyGiftBag" @close="getMasks"></daily-gift-bag>
		<inviting-friends :visibile="isInvitingFriends" @close="getMasks" @show="showInviting" ref="inviting"></inviting-friends>
		<inviting-mask :visibile="isInvitingMask" @close="showInviting"></inviting-mask>
		<receive :visibile="isReceive" @close="closeReceive" :hasDailyCoin="hasDailyCoin" :coinNum="coinNum"></receive>
	</div>
</template>

<script>
	import api from '../../api/api'
	import { Divider, InlineLoading } from 'vux'
	import Graphic from '../Common/Graphic'
	import { mapState, mapActions } from 'vuex'
	import Swipers from '../Common/Swiper'
	import LoginBonus from '../Functions/LoginBonus'
	import DailyGiftBag from '../Functions/DailyGiftBag'
	import InvitingFriends from '../Functions/InvitingFriends'
	import InvitingMask from './InvitingMask'
  import Receive from '../Functions/Receive'

	export default {
		components: {
			Graphic, Divider, Swipers, LoginBonus, DailyGiftBag, InvitingFriends, InvitingMask, Receive, InlineLoading
		},
		data () {
			return {
				newsList: [],
				functionButtons: [{ title: '每日礼包', imgName: 'btn_1' }, 
				{ title: '邀请有礼', imgName: 'btn_2' }, 
				{ title: '新手福利', imgName: 'btn_3' }, 
				// { title: '活动', imgName: 'btn_4', link: '/News' }, 
				{ title: '游戏指引', imgName: 'btn_4', link: '/News' }, 
				// { title: '玩家秀', imgName: 'btn_5', link: '/GamePlayerShow' }
				],
				search: {
					start: 0,
					limit: 10,
					categoryId: '',
				},
				isLoginBonus: false,
				isDailyGiftBag: false,
				isInvitingFriends: false,
				isInvitingMask: false,
				isReceive: false,
				coinNum: 0,
        hasDailyCoin: false,
        height: 0,
        total: 0,
				sw: true,
				shows: true,
				tabActive: 0,
				tabWidth: 0,
				translateWidth: 0,
				fixedTab: false,
				main: null,
				hdWidth: 0,
				swiperIndex: 0,
				// channelsTypeList: [],
				channelsTypeList: [{name: '全部', id: ''}],
				id: null,
				header: null,
				tabs: null,
				isHidden: false,
				scrollTop: 0,
				isScrollTop: false,
				enabled: false,
			}
		},

		mounted() {
			this.shareInvitationReward()
			window.addEventListener('scroll', () => {
				setTimeout(() => {
					this.setScroll()
				}, 300)
				this.tabFixed()
			})
			this.tabs = document.getElementById('tabs')
			this.header = document.getElementById('header')

			this.height = window.innerHeight
			this.hdWidth = window.innerWidth * 4
      window.onresize = () => {
				this.height = window.innerHeight
				this.hdWidth = window.innerWidth * 4
			}

			this.from()

			// let share = this.getItem('share')
			// if (share) {
			// 	this.isInvitingFriends = true
			// }
      
			let start = Number(this.getItem('start'))
			if (start) this.search.start = start

			let total = Number(this.getItem('total'))
			if (total) this.total = total

			let channelsTypeList = JSON.parse(this.getItem('channelsTypeList'))
			if (channelsTypeList && channelsTypeList.length > 1) {
				this.channelsTypeList = channelsTypeList
				this.lsChannelsType()
			} else {
				this.getChannelsType()
			}
		},
		beforeDestroy() {
			this.local()
			localStorage.removeItem('share')
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
				userInfo: function (state) {
					let localMember = JSON.parse(window.localStorage.getItem('member'))
					if (!state.member.name && localMember) {
						this.$store.commit('updateMember', {member: localMember})
					}
					return state.member
				},
				
			})
		},
		methods: {
			...mapActions([
				'updateMember', 'updateSum'
			]),
			
			setItem (string, value) {
				localStorage.setItem(string, value)
			},

			getItem (string) {
				return localStorage.getItem(string)
			},

			local () {
				this.setItem('channelsType', JSON.stringify(this.channelsTypeList[this.swiperIndex]))
				this.setItem('scrollTop', this.scrollTop)
				if (this.newsList.length > 0) this.setItem('newsList', JSON.stringify(this.newsList))
				if (this.channelsTypeList.length > 0) this.setItem('channelsTypeList', JSON.stringify(this.channelsTypeList))
				if (this.search.start !== 0) this.setItem('start', this.search.start)
				if (this.total !== 0) this.setItem('total', this.total)
			},

			tabFixed () {
				if (this.tabs.offsetTop <= (document.documentElement.scrollTop||document.body.scrollTop) + this.header.offsetHeight) {
					this.fixedTab = true
				} else {
					this.fixedTab = false
				}
			},

			clickActive (index, item) {
				this.tabActive = index
				this.swiperIndex = index
				this.translateWidth = this.tabWidth * index
				console.log(item)
				this.channels(item.id)
			},

			closeReceive () {
				this.isReceive = !this.isReceive
			},

			to (id) {
				this.loading(true, '加载中')
				this.$router.push(`/index/detail/${id}`)
			},

			// 每日礼包
			from () {
				let dailySignInSuccess = this.getItem('dailySignInSuccess')
				if (!dailySignInSuccess) {
					let from = this.util.getUrlParam('from')
					if (from === 'dailySignInSuccess') {
						this.coinNum = Number(this.util.getUrlParam('coinNum'))
						this.setItem('dailySignInSuccess', true)
						if (this.util.getUrlParam('hasDailyCoin') === 'true') {
							this.hasDailyCoin = true
						} else {
							this.hasDailyCoin = false
						}
						this.closeReceive()
					}
				}
			},

			show (val) {
				switch (val) {
					case 0:
						this.isHidden = true
						this.isDailyGiftBag = !this.isDailyGiftBag
						break
					case 1:
						this.isInvitingFriends = !this.isInvitingFriends
						break
					case 2:
						this.isHidden = true
						this.isLoginBonus = true
						break
					case 3:
						// this.$router.push('/News')
						window.location.href = 'http://mp.weixin.qq.com/s/rEV9658Pa5x_RelU_U4J4Q'
						break
				}
			},
			getMasks (val) {
				switch (val) {
					case 0:
						this.isHidden = false
						this.isDailyGiftBag = !this.isDailyGiftBag
						break
					case 1:
						this.isInvitingFriends = !this.isInvitingFriends
						break
					case 2:
						this.isHidden = false
						this.isLoginBonus = false
						break
				}
			},

			showInviting (val) {
				this.isInvitingMask = val
			},

			// 缓存的频道分类
			lsChannelsType () {
				this.tabWidth = window.innerWidth / this.channelsTypeList.length
				let channelsType = JSON.parse(this.getItem('channelsType'))
				if (channelsType) {
					this.channelsTypeList.map((item, index) => {
						if (item.id === channelsType.id) {
							this.tabActive = index
							this.swiperIndex = index
							this.translateWidth = this.tabWidth * index
						}
					})

					let newsList = JSON.parse(this.getItem('newsList'))
					if (newsList.length > 0) {
						newsList.map(item => {
							this.newsList.push(item)
						})
					}

					this.isScrollTop = true
					let scrollTop = Number(this.getItem('scrollTop'))
					if (this.isScrollTop && scrollTop !== 0) {
						setTimeout(() => {
							document.documentElement.scrollTop = scrollTop
							document.body.scrollTop = scrollTop
						}, 100)
					}
					this.isScrollTop = false
				}
			},

			// 频道分类
			getChannelsType () {
				api.getChannelsType(1).then(res => {
					if (res.data.errCode === 0) {
						console.log('getChannelsType')
						console.log(res.data)
						res.data.list.map(item => {
							this.channelsTypeList.push(item)
						})

						this.tabWidth = window.innerWidth / this.channelsTypeList.length
						this.channels(this.channelsTypeList[0].id)
					}
				})
			},

			// 获取娃娃机列表
      channels (id) {
				if (id && this.id === id)	return
				else this.id = id

				if (id || id === '') {
					this.search.categoryId = id
					this.search.start = 0
					this.newsList = []
					this.shows = false
				}

				api.channelsV1(this.search).then(res => {
					console.log(new Date())
					if (res.data.errCode === 0) {
						console.log(res.data)
            this.total = res.data.count

						// if (id || id === '') this.newsList = []
						if (this.total === 0) this.shows = true
						for (var i = 0; i < res.data.list.length; i++) {
							this.newsList.push(res.data.list[i])
						}
						if (this.total <= this.newsList.length) this.shows = true
						this.sw = true
		
						this.search.start = this.search.start + this.search.limit
						// this.isScrollTop = false
					}
				})
			},

      setScroll () {
				this.scrollTop = document.documentElement.scrollTop||document.body.scrollTop
				// console.log(this.scrollTop)
				if (document.body.scrollHeight - window.innerHeight - 500 <= document.documentElement.scrollTop||document.body.scrollTop) {
				// if (document.body.scrollHeight - window.innerHeight - 500 <= window..scrollTop) {
					if (this.total <= this.newsList.length) {
						this.shows = true
					} else {
						if (this.sw === true) {
							this.sw = false
							this.shows = false
							this.channels()
						}
					}
				}
			},

			shareInvitationReward () {
				api.shareInvitationReward().then(res => {
					if (res.data.errCode === 0) {
						this.enabled = res.data.result.enabled
					}
				})
			}
		}
	}
</script>

<style lang="less">
	.hidden {
		overflow: hidden;
	}
  .main {
		background: url(http://wawa-1255600302.file.myqcloud.com/images/bg2.png);
		background-size: 100%;
		height: 100%;
		// overflow: scroll;
		// -webkit-overflow-scrolling: touch;
		.header {
			// padding: 0 1.5rem;
			display: flex;
			justify-content: space-between;
			height: 45px;
			align-items: center;
			position: fixed;
			width: 100%;
			z-index: 5;
			background: url(http://wawa-1255600302.file.myqcloud.com/images/bg2.png);
			background-size: 100%;
			.user-info {
				display: inline-flex;
				align-items: center;
				position: relative;
				padding-left: 1.5rem;
				img {
					width: 3.5rem;
					height: 3.5rem;
					border-radius: 50%;
					margin-right: 5px;
				}
				.vip {
					position: absolute;
					width: 1.6rem;
					height: 1.6rem;
					background: rgb(255, 204, 0);
					color: #fff;
					border-radius: 50%;
					font-size: 1rem;
					line-height: 2rem;
					text-align: center;
					left: 2.6rem;
					top: 0;
				}
				p {
					display: inline-flex;
					// align-items: center;
					color: #fff;
					font-size: 1.6rem;
					line-height: 2rem;
					flex-flow: column;
					.icon {
						width: 25px;
						height: 25px;
						margin-right: 5px;
					}
				}
			}
			.function {
				position: relative;
				display: inline-flex;
				padding-right: 1.5rem;
				.img {
					width: 3.2rem;
					line-height: 0;
					&:not(:last-child) {
						margin-right: 1.6rem;
					}
					img {
						width: 100%;
					}
					
					@keyframes shake {
						0% {
							transform: rotateZ(0deg);
						}
						2.5% {
							transform: rotateZ(25deg);
						}
						7.5% {
							transform: rotateZ(-25deg);
						}
						12.5% {
							transform: rotateZ(25deg);
						}
						7.5% {
							transform: rotateZ(-25deg);
						}
						20% {
							transform: rotateZ(0deg);
						}
						100% {
							transform: rotateZ(0deg);
						}
					}
					.lihe {
						animation: shake 2s linear 0s infinite normal;
					}
				}
			}
		}
		.main-box {
			background: url(http://wawa-1255600302.file.myqcloud.com/images/bg2.png);
			background-size: 100%;
			overflow-y: scroll;
			padding-top: 5rem;
			.function-button {
				display: flex;
				background: #fff;
				justify-content: space-around;
				padding: 1.5rem 0;
				.button-item {
					display: inline-flex;
					flex-flow: column;
					justify-content: center;
					align-items: center;
					font-size: 1.4rem;
					line-height: 1;
					img {
						width: 4rem;
						height: 4rem;
						margin-bottom: 10px;
					}
				}	
			}
			.tabs {
				position: relative;
				background: #fff;
				height: 44px;
				.tabs_wrap {
					height: 44px;
					position: absolute;
					left: 0;
					right: 0;
					overflow: hidden;
					z-index: 5;
					&:after {
						content: '';
						position: absolute;
						top: 0;
						left: 0;
						width: 200%;
						height: 200%;
						transform: scale(0.5);
						transform-origin: 0 0;
						pointer-events: none;
						box-sizing: border-box;
						border: 0 solid #e5e5e5;
						border-width: 1px 0;
						z-index: 2;
					}
					.tabs_nav {
						display: flex;
						user-select: none;
						background-color: #fff;
						position: relative;
						height: 100%;
						padding-bottom: 15px;
						box-sizing: content-box;
						.tabs_nav-bar {
							z-index: 0;
							left: 0;
							bottom: 15px;
							height: 2px;
							position: absolute;
							background-color: #f44;
							transition-duration: .2s;
						}
						.tab-item {
							z-index: 5;
							flex: 1;
							cursor: pointer;
							// padding: 0 5px;
							height: 44px;
							font-size: 1.4rem;
							position: relative;
							color: #333;
							line-height: 44px;
							text-align: center;
							box-sizing: border-box;
							min-width: 0;
							white-space: nowrap;
							&:active {
								background-color: #e8e8e8;
							}
						}
						.tab-active {
							color: #f44;
						}
					}
					.tabs_nav--line {
						height: 100%;
						padding-bottom: 15px;
						box-sizing: content-box;
					}
				}
				.fixed {
					position: fixed;
					top: 45px;
					// left: 0;
					// width: 100%;
					background: #fff;
					// z-index: 5;
				}
			}
			.listImg {
				display: flex;
				flex-flow: row wrap;
				// overflow: scroll;
			}
		}
  }
</style>