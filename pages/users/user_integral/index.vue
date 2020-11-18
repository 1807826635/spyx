<template>
	<view>
		<view class='integral-details'>
			<view class='header'>
				<view class='currentScore'>当前积分</view>
				<view class="scoreNum">{{userInfo.integral}}</view>
				<view class='line'></view>
				<view class='nav acea-row'>
					<view class='item'>
						<view class='num'>{{userInfo.sum_integral}}</view>
						<view>累计积分</view>
					</view>
					<view class='item'>
						<view class='num'>{{userInfo.deduction_integral}}</view>
						<view>累计消费</view>
					</view>
					<view class='item'>
						<view class='num'>{{userInfo.today_integral}}</view>
						<view>今日获得</view>
					</view>
				</view>
			</view>
			<view class='wrapper'>
				<view class='nav acea-row'>
					<view class='item acea-row row-center-wrapper' :class='current==index?"on":""' v-for="(item,index) in navList" :key='index'
					 @click='nav(index)'><text class='iconfont' :class="item.icon"></text>{{item.name}}</view>
				</view>
				<view class='list' :hidden='current!=0'>
					<view class='tip acea-row row-middle'><text class='iconfont icon-shuoming'></text>提示：积分数值的高低会直接影响您的会员等级</view>
					<view class='item acea-row row-between-wrapper' v-for="(item,index) in integralList" :key="index">
						<view>
							<view class='state'>{{item.mark}}</view>
							<view>{{item.add_time}}</view>
						</view>
						<view class='num font-color' v-if="item.pm">+{{item.number}}</view>
						<view class='num' v-else>-{{item.number}}</view>
					</view>
					<view class='loadingicon acea-row row-center-wrapper' v-if="integralList.length>0">
						<text class='loading iconfont icon-jiazai' :hidden='loading==false'></text>{{loadTitle}}
					</view>
					<view v-if="integralList.length == 0">
						<emptyPage title="暂无积分记录哦～"></emptyPage>
					</view>
				</view>
				<view class='list2' :hidden='current!=1'>
					<navigator class='item acea-row row-between-wrapper' hover-class='none' url='/pages/index/index'>
						<view class='pictrue'>
							<image src='http://www.runcode.pro/goods/images/score.png'></image>
						</view>
						<view class='name'>购买商品可获得积分奖励</view>
						<view class='earn'>赚积分</view>
					</navigator>
					<navigator class='item acea-row row-between-wrapper' hover-class='none' url='/pages/users/user_sgin/index'>
						<view class='pictrue'>
							<image src='http://www.runcode.pro/goods/images/score.png'></image>
						</view>
						<view class='name'>每日签到可获得积分奖励</view>
						<view class='earn'>赚积分</view>
					</navigator>
				</view>
			</view>
		</view>
		<!-- #ifdef MP -->
		<!-- <authorize @onLoadFun="onLoadFun" :isAuto="isAuto" :isShowAuth="isShowAuth" @authColse="authColse"></authorize> -->
		<!-- #endif -->
	</view>
</template>

<script>
	import { postSignUser, getIntegralList } from '@/api/user.js';
	import {
		toLogin
	} from '@/libs/login.js';
	import {
		mapGetters
	} from "vuex";
	// #ifdef MP
	import authorize from '@/components/Authorize';
	// #endif
	import emptyPage from '@/components/emptyPage.vue'
	export default {
		components: {
			// #ifdef MP
			authorize,
			// #endif
			emptyPage
		},
		data() {
			return {
				navList: [{
						'name': '分值明细',
						'icon': 'icon-mingxi'
					},
					{
						'name': '分值提升',
						'icon': 'icon-tishengfenzhi'
					}
				],
				current: 0,
				page: 1,
				limit: 10,
				integralList: [],
				userInfo:{},
				loadend: false,
				loading: false,
				loadTitle: '加载更多',
				isAuto: false, //没有授权的不会自动授权
				isShowAuth: false //是否隐藏授权
			};
		},
		computed: mapGetters(['isLogin']),
		watch:{
			isLogin:{
				handler:function(newV,oldV){
					if(newV){
						this.getUserInfo();
						this.getIntegralList();
					}
				},
				deep:true
			}
		},
		onLoad() {
			if (this.isLogin) {
				this.getUserInfo();
				this.getIntegralList();
			} else {
				toLogin();
			}
		},
		/**
		   * 页面上拉触底事件的处理函数
		   */
		  onReachBottom: function () {
		    this.getIntegralList();
		  },
		methods: {
			/**
			 * 授权回调
			 */
			onLoadFun: function() {
				this.getUserInfo();
				this.getIntegralList();
			},
			// 授权关闭
			authColse: function(e) {
				this.isShowAuth = e
			},
			getUserInfo: function() {
				let that = this;
				postSignUser({
					sign: 1,
					integral: 1,
					all: 1
				}).then(function(res) {
					that.$set(that,'userInfo',res.data);
				});
			},

			/**
			 * 获取积分明细
			 */
			getIntegralList: function() {
				let that = this;
				if (that.loading) return;
				if (that.loadend) return;
				that.loading = true;
				that.loadTitle = '';
				getIntegralList({
					page: that.page,
					limit: that.limit
				}).then(function(res) {
					let list = res.data,
						loadend = list.length < that.limit;
					that.integralList = that.$util.SplitArray(list, that.integralList);
					that.$set(that,'integralList',that.integralList);
					that.page = that.page + 1;
					that.loading = false;
					that.loadend = loadend;
					that.loadTitle = loadend ? '哼~😕我也是有底线的~' : "加载更多";
				}, function(res) {
					this.loading = false;
					that.loadTitle = '加载更多';
				});
			},
			nav: function(current) {
				this.current = current;
			}
		}
	}
</script>

<style scoped lang="scss">
	.integral-details .header {
		background-image: url('data:image/jpg;base64,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');
		background-repeat: no-repeat;
		background-size: 100% 100%;
		width: 100%;
		height: 460rpx;
		font-size: 72rpx;
		color: #fff;
		padding: 31rpx 0 45rpx 0;
		box-sizing: border-box;
		text-align: center;
		font-family: 'Guildford Pro';
	}

	.integral-details .header .currentScore {
		font-size: 26rpx;
		color: rgba(255, 255, 255, 0.8);
		text-align: center;
		margin-bottom: 11rpx;
	}
	
	.integral-details .header .scoreNum{
		font-family: "Guildford Pro";
	}
	
	.integral-details .header .line {
		width: 60rpx;
		height: 3rpx;
		background-color: #fff;
		margin: 20rpx auto 0 auto;
	}

	.integral-details .header .nav {
		font-size: 22rpx;
		color: rgba(255, 255, 255, 0.8);
		flex: 1;
		margin-top: 35rpx;
	}

	.integral-details .header .nav .item {
		width: 33.33%;
		text-align: center;
	}

	.integral-details .header .nav .item .num {
		color: #fff;
		font-size: 40rpx;
		margin-bottom: 5rpx;
		font-family: 'Guildford Pro';
	}

	.integral-details .wrapper .nav {
		flex: 1;
		width: 690rpx;
		border-radius: 20rpx 20rpx 0 0;
		margin: -96rpx auto 0 auto;
		background-color: #f7f7f7;
		height: 96rpx;
		font-size: 30rpx;
		color: #bbb;
	}

	.integral-details .wrapper .nav .item {
		text-align: center;
		width: 50%;
	}

	.integral-details .wrapper .nav .item.on {
		background-color: #fff;
		color: #4C56AA;
		font-weight: bold;
		border-radius: 20rpx 0 0 0;
	}

	.integral-details .wrapper .nav .item:nth-of-type(2).on {
		border-radius: 0 20rpx 0 0;
	}

	.integral-details .wrapper .nav .item .iconfont {
		font-size: 38rpx;
		margin-right: 10rpx;
	}

	.integral-details .wrapper .list {
		background-color: #fff;
		padding: 24rpx 30rpx;
	}

	.integral-details .wrapper .list .tip {
		font-size: 25rpx;
		width: 690rpx;
		height: 60rpx;
		border-radius: 50rpx;
		background-color: #fff5e2;
		border: 1rpx solid #ffeac1;
		color: #c8a86b;
		padding: 0 20rpx;
		box-sizing: border-box;
		margin-bottom: 24rpx;
	}

	.integral-details .wrapper .list .tip .iconfont {
		font-size: 35rpx;
		margin-right: 15rpx;
	}

	.integral-details .wrapper .list .item {
		height: 124rpx;
		border-bottom: 1rpx solid #eee;
		font-size: 24rpx;
		color: #999;
	}

	.integral-details .wrapper .list .item .state {
		font-size: 28rpx;
		color: #282828;
		margin-bottom: 8rpx;
	}

	.integral-details .wrapper .list .item .num {
		font-size: 36rpx;
		font-family: 'Guildford Pro';
	}

	.integral-details .wrapper .list2 {
		background-color: #fff;
		padding: 24rpx 0;
	}

	.integral-details .wrapper .list2 .item {
		background-image: linear-gradient(to right, #fff7e7 0%, #fffdf9 100%);
		width: 690rpx;
		height: 180rpx;
		position: relative;
		border-radius: 10rpx;
		margin: 0 auto 20rpx auto;
		padding: 0 25rpx 0 180rpx;
		box-sizing: border-box;
	}

	.integral-details .wrapper .list2 .item .pictrue {
		width: 90rpx;
		height: 150rpx;
		position: absolute;
		bottom: 0;
		left: 45rpx;
	}

	.integral-details .wrapper .list2 .item .pictrue image {
		width: 100%;
		height: 100%;
	}

	.integral-details .wrapper .list2 .item .name {
		width: 285rpx;
		font-size: 30rpx;
		font-weight: bold;
		color: #c8a86b;
	}

	.integral-details .wrapper .list2 .item .earn {
		font-size: 26rpx;
		color: #c8a86b;
		border: 2rpx solid #c8a86b;
		text-align: center;
		line-height: 52rpx;
		height: 52rpx;
		width: 160rpx;
		border-radius: 50rpx;
	}
</style>
