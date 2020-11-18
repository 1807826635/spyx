<template>
	<view class="c_monograph" :class="{borderShow:isBorader}">
		<view class="item backstage" v-if="isShow && !picList.length">
			<text class="iconfont icon-icon25201"></text>
		</view>
		<view class="item" v-for="(item,index) in picList" :key='index' v-if="picList.length && isShowModule && !isShow" @click="goDetail(item)">
			<image :src="item.img" mode="widthFix"></image>	
			<!-- <easy-loadimage mode="widthFix" :scroll-top="scrollTop" :image-src="item.img"></easy-loadimage> -->
		</view>
		<view class="item" v-for="(item,index) in picList" :key='index' v-if="picList.length && isShow">
			<image :src="item.img" mode="widthFix"></image>
		</view>
	</view>
</template>

<script>
	let app = getApp();
	import { goPage } from '@/libs/order.js';
	import easyLoadimage from '@/components/easy-loadimage/easy-loadimage.vue'
	export default {
		name: 'c_monograph',
		props: {
			dataConfig: {
				type: Object,
				default: () => {}
			},
			activeName:{
				type:String,
				default:''
			},
			scrollTop:{
				type: Number,
				default:0
			}
		},
		components:{easyLoadimage},
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
			},
			scrollTop(val){
				this.scrollTop = val;
			}
		},
		data() {
			return {
				picList: this.dataConfig.imgList.list,
				isBorader:false,
				name:this.$options.name,
				isShow:app.globalData.isIframe,
				isShowModule:this.dataConfig.isShow.val
			};
		},
		created() {},
		mounted() {},
		methods: {
			goDetail(url) {
				goPage().then(res => {
					let urls = url.info[1].value
					if(urls){
						if (urls.indexOf("http") != -1) {
							// #ifdef H5
							location.href = urls
							// #endif
						} else {
							if (['/pages/goods_cate/goods_cate', '/pages/order_addcart/order_addcart', '/pages/user/index'].indexOf(urls) ==
								-1) {
								uni.navigateTo({
									url: urls
								})
							} else {
								uni.reLaunch({
									url: urls
								})
							}
						}
					}
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.c_monograph{
		.item{
			width: 100%;
			background-color: #fff;
			image{
				width: 100%;
				height: auto;
				display: block;
			}
			&.backstage{
				height: 350rpx;
				line-height: 350rpx;
				text-align: center;
				background-color: #ccc;
				.iconfont{
					font-size: 60rpx;
				}
			}
		}
	}
</style>
