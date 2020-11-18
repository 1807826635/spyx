<template>
	<view class="uni-tabbar acea-row row-around row-middle" :class="{borderShow:isBorader}">
		<view class="uni-tabbar_item" v-for="(item,index) in tabbar" :key="index" @tap="changeTab(item)">
			<view class="uni-tabbar_icon">
				<image v-if="item.pagePath == pagePath" mode="aspectFit" :src="item.selectedIconPath"></image>
				<image v-else mode="aspectFit" :src="item.iconPath"></image>
			</view>
			<view class="uni-tabbar_label" :class="{'active': item.pagePath == pagePath}">
				{{item.name}}
			</view>
		</view>
	</view>
</template>

<script>
	let app = getApp();
	import {
		goPage
	} from '@/libs/order.js'
	export default {
		name: 'z_tabBar',
		props: {
			pagePath: null,
			dataConfig: {
				type: Object,
				default: () => {}
			},
			activeName: {
				type: String,
				default: ''
			}
		},
		watch: {
			activeName: {
				handler(nVal, oVal) {
					if (nVal == this.name && app.globalData.isIframe) {
						this.isBorader = true
					} else {
						this.isBorader = false
					}
				},
				deep: true
			}
		},
		data() {
			return {
				isBorader: false,
				name: this.$options.name,
				page: '/pages/index/index',
				showPage: false,
				containerHeight: 400,
				tabbar: ''
			};
		},
		created(){
			if(this.dataConfig){
				var obj ={
					iconPath: "http://v4.shopping.crmeb.net//uploads/attach/2020/09/20200908/d7ac557d80eb2c990c78b3d327544e34.png",
					name: "直播",
					pagePath: "/pages/live/index",
					selectedIconPath: "http://v4.shopping.crmeb.net//uploads/attach/2020/09/20200908/1075fff3e76afb0d4c34ec196ed36a06.png"
				}
				var arr = this.dataConfig.tabBarList.list.splice(2,0,obj)
				console.log(arr)
				uni.setStorageSync('tabbar', this.dataConfig.tabBarList.list);
			}
		},
		mounted() {
			this.tabbar = uni.getStorageSync('tabbar');
		},
		methods: {
			changeTab(item) {
				
				goPage().then(res => {
					this.page = item.pagePath;
					// 这里使用reLaunch关闭所有的页面，打开新的栏目页面
					uni.reLaunch({
						url: this.page
					});
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	// @supports (bottom: constant(safe-area-inset-bottom)) or (bottom: env(safe-area-inset-bottom)) { 
	//     .uni-tabbar {
	//         margin-bottom: constant(safe-area-inset-bottom);///兼容 IOS<11.2/
	//         margin-bottom: env(safe-area-inset-bottom);///兼容 IOS>11.2/
	//     }
	// }
	.uni-tabbar {
		position: fixed;
		bottom: 0;
		z-index: 9999;
		width: 100%;
		height: calc(98rpx+ constant(safe-area-inset-bottom));///兼容 IOS<11.2/
		height: calc(98rpx + env(safe-area-inset-bottom));///兼容 IOS>11.2/
		box-sizing: border-box;
		border-top: solid 1rpx #F3F3F3;
		background-color: #fff;
		box-shadow: 0px 0px 17upx 1upx rgba(206, 206, 206, 0.32);
		padding-bottom: constant(safe-area-inset-bottom);///兼容 IOS<11.2/
		padding-bottom: env(safe-area-inset-bottom);///兼容 IOS>11.2/
		
		.uni-tabbar_item {
			font-size: 20rpx;
			text-align: center;
		}

		.uni-tabbar_icon {
			height: 50rpx;
			width: 50rpx;
			text-align: center;
			margin: 0 auto;

			image {
				width: 100%;
				height: 100%;
			}
		}

		.uni-tabbar_label {
			font-size: 24rpx;
			color: #282828;

			&.active {
				color: #4B55A9;
			}
		}
	}
</style>