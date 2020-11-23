<template>
	<view class="swiperBg">
		<block>
			<view class="default" :class="{borderShow:isBorader}" v-if="isShow && !imgUrls.length">
				<text class="iconfont icon-icon25201"></text>
			</view>
			<view class="swiper circular" v-if="imgUrls.length && isShowModule && !isShow">
				<swiper :style="'height:'+ clientHeight +'rpx;'" indicator-dots="true" :autoplay="true" :circular="circular" :interval="interval" :duration="duration" indicator-color="rgba(255,255,255,0.6)" indicator-active-color="#fff">
					<block v-for="(item,index) in imgUrls" :key="index">
						<swiper-item>
							<view @click="goDetail(item)" class='slide-navigator acea-row row-between-wrapper'>
								<image :src="item.img" class="slide-image" mode="widthFix"></image>
							</view>
						</swiper-item>
					</block>
				</swiper>
			</view>
			<view class="swiper circular" :class="{borderShow:isBorader}" v-if="imgUrls.length && isShow">
				<swiper :style="'height:'+ clientHeight +'rpx;'" indicator-dots="true" :autoplay="true" :circular="circular" :interval="interval" :duration="duration" indicator-color="rgba(255,255,255,0.6)" indicator-active-color="#fff">
					<block v-for="(item,index) in imgUrls" :key="index">
						<swiper-item>
							<view @click="goDetail(item)" class='slide-navigator acea-row row-between-wrapper'>
								<image :src="item.img" class="slide-image" mode="widthFix"></image>
							</view>
						</swiper-item>
					</block>
				</swiper>
			</view>
		</block>
	</view>
</template>

<script>
	let app = getApp();
	import { goPage } from '@/libs/order.js';
	export default {
		name: 'b_swiperBg',
		props: {
			dataConfig: {
				type: Object,
				default: () => {}
			},
			activeName:{
				type:String,
				default:''
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
				indicatorDots: false,
				circular: true,
				autoplay: true,
				interval: 3000,
				duration: 500,
				imgUrls: this.dataConfig.imgList.list, //图片轮播数据
				isBorader:false,
				name:this.$options.name,
				isShow:app.globalData.isIframe,
				isShowModule:this.dataConfig.isShow.val,
				clientHeight:0
			};
		},
		created() {
		},
		mounted() {
			let that = this;
			this.$nextTick(function() {
				if(that.imgUrls.length){
					uni.getImageInfo({
						// #ifdef H5
						src: that.imgUrls[0].img,
						// #endif
						// #ifdef MP
						src: that.setDomain(that.imgUrls[0].img),
						// #endif
						success: function(res) {
							let height = res.height * (750 / res.width)
							that.$set(that, 'clientHeight', height);
						},
						fail: function(error) {
							that.$set(that, 'clientHeight', 400);
						}
					})
				}
			})
		},
		methods: {
			//替换安全域名
			setDomain: function(url) {
				url = url ? url.toString() : '';
				//本地调试打开,生产请注销
				if (url.indexOf("https://") > -1) return url;
				else return url.replace('http://', 'https://');
			},
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

<style lang="scss">
	.swiperBg {
		position: relative;
		.default{
			z-index: 20;
			position: relative;
			width: 100%;
			height: 800rpx;
			margin: 0 auto;
			border-radius: 10rpx;
			background-color: #ccc;
			text-align: center;
			line-height: 800rpx;
			.iconfont{
				font-size: 80rpx;
			}
		}
		.colorBg {
			position: absolute;
			left: 0;
			top: 0;
			height: 80rpx;
			width: 100%;
			background: linear-gradient(90deg, $bg-star 50%, $bg-end 100%);
		}

		.swiper {
			z-index: 20;
			position: relative;
			overflow: hidden;

			/* 设置圆角 */
			&.fillet {
				border-radius: 10rpx;

				image {
					border-radius: 10rpx;
				}
			}

			swiper,
			.swiper-item,
			image {
				width: 100%;
				display: block;
			}

			// 圆形指示点
			&.circular {
				/deep/.uni-swiper-dot {
					width: 10rpx !important;
					height: 10rpx !important;
					background: rgba(0, 0, 0, .4) !important
				}

				/deep/.uni-swiper-dot-active {
					background: #DF828C !important;
					width: 30rpx !important;
					border-radius: 5rpx;
				}
			}

			// 方形指示点
			&.square {
				/deep/.uni-swiper-dot {
					width: 20rpx !important;
					height: 5rpx !important;
					border-radius: 3rpx;
					background: rgba(0, 0, 0, .4) !important
				}

				/deep/.uni-swiper-dot-active {
					background: #fff !important
				}
			}
		}
	}
	.item-img image{
		display: block;
		width: 100%;
	}
</style>
