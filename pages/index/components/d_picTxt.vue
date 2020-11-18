<template>
	<view class="txtPic" :class="{borderShow:isBorader}">
		<view class="ueditor acea-row row-center-wrapper" v-if="isShow && !description">
			<text class="iconfont icon-icon_im_keyboard"></text>
		</view>
		<jyf-parser :html="description" ref="article" :tag-style="tagStyle" v-if="description && isShowModule && !isShow"></jyf-parser>
		<jyf-parser :html="description" ref="article" :tag-style="tagStyle" v-if="description && isShow"></jyf-parser>
	</view>
</template>

<script>
	let app = getApp();
	import parser from "@/components/jyf-parser/jyf-parser";
	import { goPage } from '@/libs/order.js';
	export default {
		name: 'd_picTxt',
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
		components: {
			"jyf-parser": parser
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
				description: this.dataConfig.richText.val,
				isBorader:false,
				name:this.$options.name,
				isShow:app.globalData.isIframe,
				isShowModule:this.dataConfig.isShow.val,
				tagStyle: {
					img: 'width:100%;display:block',
					video: 'width: 100% !important;'
				}
			};
		},
		created() {
		},
		mounted() {},
		methods: {
		}
	}
</script>

<style scoped lang="scss">
	.txtPic{
		.ueditor{
			width: 100%;
			height: 300rpx;
			background-color: #ccc;
			.iconfont{
				font-size: 50rpx;
			}
		}
	}
</style>
