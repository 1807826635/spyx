<template>
	<view>
		<form @submit="formSubmit" report-submit='true'>
			<view class='personal-data'>
				<view class="wrapper">
					<view class="title">管理我的账号</view>
					<view class="wrapList">
						<view class="item acea-row row-between-wrapper" :class="item.uid === userInfo.uid ? 'on' : ''" v-for="(item,index) in switchUserInfo"
						 :key="index" @click='switchAccounts(index)'>
							<view class="picTxt acea-row row-between-wrapper">
								<view class="pictrue" @click.stop='uploadpic' v-if='item.uid === userInfo.uid'>
									<image :src='item.avatar'></image>
									<image src='http://www.runcode.pro/goods/images/alter.png' class="alter"></image>
								</view>
								<view class="pictrue" v-else>
									<image :src='item.avatar'></image>
								</view>
								<view class="text">
									<view class="name line1">{{ item.nickname }}</view>
									<view class="phone" v-if="item.phone && item.user_type !='h5'">绑定手机号：{{ item.phone }}</view>
									<view class="phone" v-else-if="item.phone && item.user_type =='h5'">账号：{{ item.phone }}</view>
									<view class="phone" v-else>暂未绑定手机号</view>
								</view>
							</view>
							<view class="currentBnt acea-row row-center-wrapper font-color-violet" v-if='item.uid === userInfo.uid'>
								当前账号
							</view>
							<view class="bnt font-color-violet acea-row row-center-wrapper" v-else>
								使用账号
							</view>
						</view>
					</view>
				</view>
				<view class='list'>
					<view class='item acea-row row-between-wrapper'>
						<view>昵称</view>
						<view class='input'><input type='text' name='nickname' :value='userInfo.nickname'></input></view>
					</view>
					<view class='item acea-row row-between-wrapper'>
						<view>手机号码</view>
						<navigator url="/pages/users/user_phone/index" hover-class="none" class="input" v-if="!userInfo.phone">
							点击绑定手机号<text class="iconfont icon-xiangyou"></text>
						</navigator>
						<view class='input acea-row row-between-wrapper' v-else>
							<input type='text' disabled='true' name='phone' :value='userInfo.phone' class='id'></input>
							<text class='iconfont icon-suozi'></text>
						</view>
					</view>
					<view class='item acea-row row-between-wrapper'>
						<view>ID号</view>
						<view class='input acea-row row-between-wrapper'>
							<input type='text' :value='userInfo.uid' disabled='true' class='id'></input>
							<text class='iconfont icon-suozi'></text>
						</view>
					</view>
					<!-- #ifdef MP -->
					<view class='item acea-row row-between-wrapper'>
						<view>权限设置</view>
						<view class="input" @click="Setting">
							点击管理<text class="iconfont icon-xiangyou"></text>
						</view>
					</view>
					<!-- #endif -->
					<!-- #ifdef H5 -->
					<view class="item acea-row row-between-wrapper" v-if="userInfo.phone && !this.$wechat.isWeixin()">
						<view>密码</view>
						<navigator url="/pages/users/user_pwd_edit/index" hover-class="none" class="input">
							点击修改密码<text class="iconfont icon-xiangyou"></text>
						</navigator>
					</view>
					<!-- #endif -->
				</view>
				<button class='modifyBnt bg-color' formType="submit">保存修改</button>
				<!-- #ifdef H5 -->
				<view class="logOut cart-color acea-row row-center-wrapper" @click="outLogin" v-if="!this.$wechat.isWeixin()">退出登录</view>
				<!-- #endif -->
			</view>
		</form>
		<!-- #ifdef MP -->
		<!-- <authorize @onLoadFun="onLoadFun" :isAuto="isAuto" :isShowAuth="isShowAuth" @authColse="authColse"></authorize> -->
		<!-- #endif -->
	</view>
</template>

<script>
	import {
		getUserInfo,
		userEdit,
		getLogout
	} from '@/api/user.js';
	import {
		switchH5Login
	} from '@/api/api.js';
	import {
		toLogin
	} from '@/libs/login.js';
	import {
		mapGetters
	} from "vuex";
	import dayjs from "@/plugin/dayjs/dayjs.min.js";
	// #ifdef MP
	import authorize from '@/components/Authorize';
	// #endif
	export default {
		components: {
			// #ifdef MP
			authorize
			// #endif
		},
		data() {
			return {
				userInfo: {},
				loginType: 'h5', //app.globalData.loginType
				userIndex: 0,
				switchUserInfo: [],
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
					}
				},
				deep:true
			}
		},
		onLoad() {
			if (this.isLogin) {
				this.getUserInfo();
			} else {
				toLogin();
			}
		},
		methods: {
			/**
			 * 授权回调
			 */
			onLoadFun: function() {
				this.getUserInfo();
			},
			// 授权关闭
			authColse: function(e) {
				this.isShowAuth = e
			},
			/**
			 * 小程序设置
			 */
			Setting: function() {
				uni.openSetting({
					success: function(res) {
						console.log(res.authSetting)
					}
				});
			},
			switchAccounts: function(index) {
				let userInfo = this.switchUserInfo[index],
					that = this;
				that.userIndex = index;
				if (that.switchUserInfo.length <= 1) return true;
				if (userInfo === undefined) return that.$util.Tips({
					title: '切换的账号不存在'
				});
				if (userInfo.user_type === 'h5') {
					uni.showLoading({
						title: '正在切换中'
					});
					switchH5Login().then(res => {
						uni.hideLoading();
						that.$store.commit("LOGIN", {
							'token': res.data.token,
							'time': this.$Cache.strTotime(res.data.expires_time) - this.$Cache.time()
						});
						that.getUserInfo();

					}).catch(err => {
						uni.hideLoading();
						return that.$util.Tips({
							title: err
						});
					})
				} else {
					that.$store.commit("LOGOUT");
					uni.showLoading({
						title: '正在切换中'
					});
					toLogin();
				}
			},
			/**
			 * 退出登录
			 * 
			 */
			outLogin: function() {
				let that = this;
				if (that.loginType == 'h5') {
					uni.showModal({
					    title: '提示',
					    content: '确认退出登录?',
					    success: function (res) {
					        if (res.confirm) {
					            getLogout()
					              .then(res => {
					                that.$store.commit("LOGOUT");
									uni.reLaunch({
										url: '/pages/index/index'
									})
					              })
					              .catch(err => {
					                console.log(err);
					              });
					        } else if (res.cancel) {
					            console.log('用户点击取消');
					        }
					    }
					});	
				}
			},
			/**
			 * 获取用户详情
			 */
			getUserInfo: function() {
				let that = this;
				getUserInfo().then(res => {
					that.$set(that, 'userInfo', res.data);
					let switchUserInfo = res.data.switchUserInfo || [];
					for (let i = 0; i < switchUserInfo.length; i++) {
						if (switchUserInfo[i].uid == that.userInfo.uid) that.userIndex = i;
						// 切割h5用户；user_type状态：h5、routine（小程序）、wechat（公众号）；注：只有h5未注册手机号时，h5才可和小程序或是公众号数据想通；
						//#ifdef H5
						if (
							!that.$wechat.isWeixin() &&
							switchUserInfo[i].user_type != "h5" &&
							switchUserInfo[i].phone === ""
						)
							switchUserInfo.splice(i, 1);
						//#endif
					}
					that.$set(that, "switchUserInfo", switchUserInfo);
				});
			},
			/**
			 * 上传文件
			 * 
			 */
			uploadpic: function() {
				let that = this;
				that.$util.uploadImageOne('upload/image', function(res){
					let userInfo = that.switchUserInfo[that.userIndex];
					if (userInfo !== undefined) {
						userInfo.avatar = res.data.url;
					}
					that.switchUserInfo[that.userIndex] = userInfo;
					that.$set(that,'switchUserInfo',that.switchUserInfo);
				});
			},

			/**
			 * 提交修改
			 */
			formSubmit: function(e) {
				let that = this,
					value = e.detail.value,
					userInfo = that.switchUserInfo[that.userIndex];
				if (!value.nickname) return that.$util.Tips({
					title: '用户姓名不能为空'
				});
				value.avatar = userInfo.avatar;
				userEdit(value).then(res => {
					return that.$util.Tips({
						title: res.msg,
						icon: 'success'
					}, {
						tab: 3,
						url: 1
					});
				}).catch(msg => {
					return that.$util.Tips({	
						title: msg || '保存失败，您并没有修改'
					}, {
						tab: 3,
						url: 1
					});
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.personal-data .wrapper {
		margin: 10rpx 0;
		background-color: #fff;
		padding: 36rpx 30rpx 13rpx 30rpx;
	}

	.personal-data .wrapper .title {
		margin-bottom: 30rpx;
		font-size: 32rpx;
		color: #282828;
	}

	.personal-data .wrapper .wrapList .item {
		width: 690rpx;
		height: 160rpx;
		background-color: #f8f8f8;
		border-radius: 20rpx;
		margin-bottom: 22rpx;
		padding: 0 30rpx;
		position: relative;
		border: 2rpx solid #f8f8f8;
		box-sizing:border-box;
	}

	.personal-data .wrapper .wrapList .item.on {
		border-color: #4C56AA;
		border-radius: 20rpx;
		background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAArIAAACgCAYAAADw+I85AAAACXBIWXMAAAsTAAALEwEAmpwYAAAF7WlUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4gPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNi4wLWMwMDIgNzkuMTY0NDYwLCAyMDIwLzA1LzEyLTE2OjA0OjE3ICAgICAgICAiPiA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPiA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6c3RFdnQ9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZUV2ZW50IyIgeG1sbnM6ZGM9Imh0dHA6Ly9wdXJsLm9yZy9kYy9lbGVtZW50cy8xLjEvIiB4bWxuczpwaG90b3Nob3A9Imh0dHA6Ly9ucy5hZG9iZS5jb20vcGhvdG9zaG9wLzEuMC8iIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTkgKE1hY2ludG9zaCkiIHhtcDpDcmVhdGVEYXRlPSIyMDIwLTA5LTA4VDE5OjA2OjM5KzA4OjAwIiB4bXA6TW9kaWZ5RGF0ZT0iMjAyMC0wOS0wOFQxOTowNzowNSswODowMCIgeG1wOk1ldGFkYXRhRGF0ZT0iMjAyMC0wOS0wOFQxOTowNzowNSswODowMCIgeG1wTU06SW5zdGFuY2VJRD0ieG1wLmlpZDo1NTBiZmRkOC0xNTEyLTlhNGQtOWQ5NS00YTdmOWU2OWRjNzIiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6M0QzNkY3N0FCNzJCMTFFOTgyNEU4QzhGQTRFRUY2REQiIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDozRDM2Rjc3QUI3MkIxMUU5ODI0RThDOEZBNEVFRjZERCIgZGM6Zm9ybWF0PSJpbWFnZS9wbmciIHBob3Rvc2hvcDpDb2xvck1vZGU9IjMiIHBob3Rvc2hvcDpJQ0NQcm9maWxlPSJzUkdCIElFQzYxOTY2LTIuMSI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOjNEMzZGNzc3QjcyQjExRTk4MjRFOEM4RkE0RUVGNkREIiBzdFJlZjpkb2N1bWVudElEPSJ4bXAuZGlkOjNEMzZGNzc4QjcyQjExRTk4MjRFOEM4RkE0RUVGNkREIi8+IDx4bXBNTTpIaXN0b3J5PiA8cmRmOlNlcT4gPHJkZjpsaSBzdEV2dDphY3Rpb249InNhdmVkIiBzdEV2dDppbnN0YW5jZUlEPSJ4bXAuaWlkOjU1MGJmZGQ4LTE1MTItOWE0ZC05ZDk1LTRhN2Y5ZTY5ZGM3MiIgc3RFdnQ6d2hlbj0iMjAyMC0wOS0wOFQxOTowNzowNSswODowMCIgc3RFdnQ6c29mdHdhcmVBZ2VudD0iQWRvYmUgUGhvdG9zaG9wIDIxLjIgKFdpbmRvd3MpIiBzdEV2dDpjaGFuZ2VkPSIvIi8+IDwvcmRmOlNlcT4gPC94bXBNTTpIaXN0b3J5PiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/Ppe8hLUAAAhbSURBVHic7d3RUuJaGobhD5RWe/alzflc5FzftLYozMEiW3YEGjWR/OR5qqhuLQiRo7cWf1YW2+02AABQzfLSJwAAAJ8hZAEAKEnIAgBQkpAFAKAkIQsAQElCFgCAkoQsAAAlCVkAAEoSsgAAlCRkAQAoScgCAFCSkAUAoCQhCwBASUIWAICShCwAACUJWQAAShKyAACUJGQBAChJyAIAUJKQBQCgJCELAEBJQhYAgJKELAAAJQlZAABKErIAAJQkZAEAKEnIAgBQkpAFAKAkIQsAQElC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");
		background-size: 100% 100%;
		background-color: #fcfcff;
		background-repeat: no-repeat;
	}

	.personal-data .wrapper .wrapList .item .picTxt {
		width: 445rpx;
	}

	.personal-data .wrapper .wrapList .item .picTxt .pictrue {
		width: 96rpx;
		height: 96rpx;
		position: relative;
	}

	.personal-data .wrapper .wrapList .item .picTxt .pictrue image {
		width: 100%;
		height: 100%;
		border-radius: 50%;
	}

	.personal-data .wrapper .wrapList .item .picTxt .pictrue .alter {
		width: 30rpx;
		height: 30rpx;
		border-radius: 50%;
		position: absolute;
		bottom: 0;
		right: 0;
	}

	.personal-data .wrapper .wrapList .item .picTxt .text {
		width: 325rpx;
	}

	.personal-data .wrapper .wrapList .item .picTxt .text .name {
		width: 100%;
		font-size: 30rpx;
		color: #282828;
	}

	.personal-data .wrapper .wrapList .item .picTxt .text .phone {
		font-size: 24rpx;
		color: #999;
		margin-top: 10rpx;
	}

	.personal-data .wrapper .wrapList .item .bnt {
		font-size: 24rpx;
		background-color: #fff;
		border-radius: 27rpx;
		width: 140rpx;
		height: 54rpx;
		border: 2rpx solid #4C56AA;
	}

	.personal-data .wrapper .wrapList .item .currentBnt {
		position: absolute;
		right: 0;
		top: 0;
		font-size: 26rpx;
		background-color: rgba(75, 86, 170, 0.1);
		width: 140rpx;
		height: 48rpx;
		border-radius: 0 20rpx 0 20rpx;
	}

	.personal-data .list {
		margin-top: 15rpx;
		background-color: #fff;
	}

	.personal-data .list .item {
		padding: 30rpx 30rpx 30rpx 0;
		border-bottom: 1rpx solid #f2f2f2;
		margin-left: 30rpx;
		font-size: 32rpx;
		color: #282828;
	}

	.personal-data .list .item .phone {
		width: 160rpx;
		height: 56rpx;
		font-size: 24rpx;
		color: #fff;
		line-height: 56rpx;
		border-radius: 32rpx
	}

	.personal-data .list .item .pictrue {
		width: 88rpx;
		height: 88rpx;
	}

	.personal-data .list .item .pictrue image {
		width: 100%;
		height: 100%;
		border-radius: 50%;
	}

	.personal-data .list .item .input {
		width: 415rpx;
		text-align: right;
		color: #868686;
	}

	.personal-data .list .item .input .id {
		width: 365rpx;
	}

	.personal-data .list .item .input .iconfont {
		font-size: 35rpx;
	}

	.personal-data .modifyBnt {
		font-size: 32rpx;
		color: #fff;
		width: 690rpx;
		height: 90rpx;
		border-radius: 50rpx;
		text-align: center;
		line-height: 90rpx;
		margin: 76rpx auto 0 auto;
	}

	.personal-data .logOut {
		font-size: 32rpx;
		text-align: center;
		width: 690rpx;
		height: 90rpx;
		border-radius: 45rpx;
		margin: 30rpx auto 0 auto;
	}
</style>
