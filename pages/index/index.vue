<template>
	<view>
		<!-- #ifdef H5 -->
		<view v-for="(item, index) in styleConfig" :key="index" @click="bindParent(item)">
			<component :is="item.name" :index="index" :dataConfig="item" :activeName="activeName" :scrollTop="scrollTop" :pagePath="'/pages/index/index'"></component>
		</view>
		<!-- #endif -->
		<view @click="logouts">退出</view>
		<!-- #ifdef MP -->
		<block v-for="(item,index) in styleConfig" :key='index'>
			<b_swiperBg v-if='item.name=="b_swiperBg"' :dataConfig="item"></b_swiperBg>
			<c_monograph v-if='item.name=="c_monograph"' :dataConfig="item" :scrollTop="scrollTop"></c_monograph>
			<d_picTxt v-if='item.name=="d_picTxt"' :dataConfig="item"></d_picTxt>
			<z_tabBar v-if='item.name=="z_tabBar"' :dataConfig="item" :pagePath="'/pages/index/index'"></z_tabBar>
		</block>
		<!-- #endif -->
		<view class="uni-p-b-98"></view>
	</view>
</template>
<script>
	let app = getApp();
	// #ifdef MP
	import b_swiperBg from './components/b_swiperBg';
	import c_monograph from './components/c_monograph';
	import d_picTxt from './components/d_picTxt';
	import z_tabBar from './components/z_tabBar';
	import {SUBSCRIBE_MESSAGE} from '@/config/cache';
	import { getTemlIds } from '@/api/api.js';
	// #endif
	// #ifdef H5
	import mConfig from './components/index.js';
	import {getShare} from '@/api/public.js';
	// #endif
	import {
		getDiy,logout
	} from '@/api/api.js';
	export default {
		components: {
			// #ifdef H5
			...mConfig,
			// #endif
			// #ifdef MP
			b_swiperBg,
			c_monograph,
			d_picTxt,
			z_tabBar,
			// #endif
		},
		data() {
			return {
				styleConfig: [],
				isIframe: false,
				activeName: '',
				scrollTop:  0
			};
		},
		onLoad() {
			let that = this;
			
			// #ifdef H5
			if (that.$wechat.isWeixin()) {
				that.$wechat.location().then(res=>{
					uni.setStorageSync('user_latitude', res.latitude);
					uni.setStorageSync('user_longitude', res.longitude);
				})
			}else{
			// #endif	
				uni.getLocation({
					type: 'wgs84',
					success: function(res) {
						try {
							uni.setStorageSync('user_latitude', res.latitude);
							uni.setStorageSync('user_longitude', res.longitude);
						} catch {}
					}
				});
			// #ifdef H5	
			}
			// #endif
			
			that.diyData();
			// #ifdef H5
			that.setOpenShare();
			// #endif
			// #ifdef MP
			that.getTemlIds();
			// #endif
		},
		onShow() {},
		mounted() {},
		methods: {
      logouts(){
        logout()
			},
			// 对象转数组
			objToArr(data) {
				let obj = Object.keys(data);

				let m = obj.map(function(key) {
					data[key].name = key;
					return data[key];
				});
				return m;
			},
			diyData() {
				let that = this;
				getDiy().then(res => {
					that.styleConfig = that.objToArr(res.data);
				});
			},
			bindParent(item) {
				this.activeName = item.name;
				if (app.globalData.isIframe) {
					window.parent.postMessage({
							name: item.name,
							params: {}
						},
						'*'
					);
					return;
				}
			},
			onLoadFun() {},
			// #ifdef MP
			getTemlIds() {
				let messageTmplIds = wx.getStorageSync(SUBSCRIBE_MESSAGE);
				if (!messageTmplIds) {
					getTemlIds().then(res => {
						if (res.data) wx.setStorageSync(SUBSCRIBE_MESSAGE, JSON.stringify(res.data));
					});
				}
			},
			// #endif
			// #ifdef H5
			// 微信分享；
			setOpenShare: function() {
				let that = this;
				if (that.$wechat.isWeixin()) {
					getShare().then(res => {
						let data = res.data.data;
						let configAppMessage = {
							desc: data.synopsis,
							title: data.title,
							link: location.href,
							imgUrl: data.img
						};
						that.$wechat.wechatEvevt(['updateAppMessageShareData', 'updateTimelineShareData'], configAppMessage);
					});
				}
			}
			// #endif
		},
		onReachBottom: function() {
		},
		onPageScroll({scrollTop}) {
		    // 传入scrollTop值并触发所有easy-loadimage组件下的滚动监听事件
		    this.scrollTop = scrollTop;
		}
	};
</script>

<style lang="scss">
	page{
		background-color: #fff;
	}
	.product-window {
		z-index: 1000 !important;
	}
</style>
