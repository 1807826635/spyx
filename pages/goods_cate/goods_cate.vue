<template>
	<view class="productSort">
		<view class='nav acea-row row-middle'>
			<scroll-view class="scroll-view_x" scroll-x scroll-with-animation :scroll-left="scrollLeft" style="width:auto;overflow:hidden;">
				<view class='item' v-for="(item,index) in navLists" :key='index' :class='active==index?"on":""' @click='tabSelect(index,item.id)'
				 :id="'id'+index">
					<view>{{item.cate_name}}</view>
					<view class='line' v-if="active==index"></view>
				</view>
			</scroll-view>
		</view>
		<view class='list acea-row row-between-wrapper'>
			<view class='item' v-for="(item,index) in productList" :key="index" @click="godDetail(item)">
				<view class='pictrue'>
					<image :src='item.image'></image>
					<span class="pictrue_log_class pictrue_log_big" v-if="item.activity && item.activity.type === '1'">秒杀</span>
					<span class="pictrue_log_class pictrue_log_big" v-if="item.activity && item.activity.type === '2'">砍价</span>
					<span class="pictrue_log_class pictrue_log_big" v-if="item.activity && item.activity.type === '3'">拼团</span>
				</view>
				<view class='text'>
					<view class='name line1'>{{item.store_name}}</view>
					<view class='money font-color'>￥<text class='num'>{{item.price}}</text></view>
					<view class='vip acea-row row-between-wrapper'>
						<view class='vip-money' v-if="item.vip_price && item.vip_price > 0">￥{{item.vip_price}}
							<image :src='isrc+"vip.png"'></image>
						</view>
						<view>已售{{item.sales}}件</view>
					</view>
				</view>
			</view>
			<view class='loadingicon acea-row row-center-wrapper' v-if="productList.length">
				<text class='loading iconfont icon-jiazai' :hidden='loading==false'></text>{{loadTitle}}
			</view>
		</view>
		<view class='noCommodity' v-if="productList.length==0 && page > 1">
			<view class='pictrue'>
				<image :src='isrc+"noShopper.png"'></image>
			</view>
			<recommend :hostProduct="hostProduct"></recommend>
		</view>
		<view class="uni-p-b-98"></view>
		<z_tabBar :pagePath="'/pages/goods_cate/goods_cate'"></z_tabBar>
	</view>
</template>

<script>
	import z_tabBar from "@/pages/index/components/z_tabBar.vue"
	import {
		getCategoryList,
		getProductslist,
		getProductHot
	} from '@/api/store.js';
	import {
		goShopDetail
	} from '@/libs/order.js'
	import recommend from '@/components/recommend';
	import {
		mapGetters
	} from "vuex";
	export default {
		computed: mapGetters(['uid']),
		components: {
			recommend,
			z_tabBar
		},
		data() {
			return {
				isrc:this.$config.imgsrc,
				navLists: [],
				productList: [],
				scrollLeft: 0,
				active: 0,
				loading: false,
				loadend: false,
				loadTitle: '加载更多',
				page: 1,
				limit: 10,
				cid: 0,
				hostProduct: []
			}
		},
		onLoad() {
			this.getAllCategory();
			this.getProductList();
			this.get_host_product();
		},
		methods: {
			// 去详情页
			godDetail(item) {
				goShopDetail(item, this.uid).then(res => {
					uni.navigateTo({
						url: `/pages/goods_details/index?id=${item.id}`
					})
				})
			},
			tabSelect(index, id) {
				this.active = index;
				const query = uni.createSelectorQuery().in(this);
				query.select('#id' + index).boundingClientRect(data => {
					this.scrollLeft = (index - 1) * data.width;
				}).exec();
				this.cid = id;
				this.loadend = false;
				this.page = 1;
				this.$set(this, 'productList', [])
				this.getProductList();
			},
			getAllCategory: function() {
				let that = this;
				getCategoryList().then(res => {
					res.data.unshift({
						'id': 0,
						'cate_name': '全部'
					});
					that.navLists = res.data;
				})
			},
			getProductList: function() {
				let that = this;
				if (that.loadend) return;
				if (that.loading) return;
				that.loading = true;
				that.loadTitle = '';
				getProductslist({
					page: that.page,
					limit: that.limit,
					cid: that.cid
				}).then(res => {
					let list = res.data,
						loadend = list.length < that.limit;
					that.productList = that.$util.SplitArray(list, that.productList);
					that.$set(that, 'productList', that.productList);
					that.loading = false;
					that.loadend = loadend;
					that.loadTitle = loadend ? "😕人家是有底线的~~" : "加载更多";
					that.page = that.page + 1;
				}).catch(err => {
					that.loading = false,
						that.loadTitle = '加载更多'
				});
			},
			/**
			 * 获取我的推荐
			 */
			get_host_product: function() {
				let that = this;
				getProductHot(1, 20).then(res => {
					that.$set(that, 'hostProduct', res.data)
				});
			},
		},
		onReachBottom() {
			this.getProductList();
		}
	}
</script>

<style scoped lang="scss">
	.productSort {
		.nav {
			padding: 0 30rpx;
			width: 100%;
			white-space: nowrap;
			box-sizing: border-box;
			height: 86rpx;
			background-color: #fff;
			position: fixed;
			top: 0;
			left: 0;
			z-index: 9;

			.item {
				display: inline-block;
				font-size: 30rpx;
				color: #282828;
				padding-right: 46rpx;

				&.on {
					color: #4B56AA;
					font-weight: bold;
				}

				.line {
					width: 40rpx;
					height: 4rpx;
					background-color: #4B56AA;
					margin: 10rpx auto 0 auto;
				}
			}
		}

		.list {
			margin-top: 86rpx;
			padding: 0 20rpx;

			.item {
				width: 345rpx;
				margin-top: 20rpx;
				background-color: #fff;
				border-radius: 20rpx;

				.pictrue {
					position: relative;
					width: 100%;
					height: 345rpx;

					image {
						width: 100%;
						height: 100%;
						border-radius: 20rpx 20rpx 0 0;
					}
				}

				.text {
					padding: 20rpx 17rpx 26rpx 17rpx;
					font-size: 30rpx;
					color: #222;

					.money {
						font-size: 26rpx;
						font-weight: bold;
						margin-top: 8rpx;

						.num {
							font-size: 34rpx;
						}
					}

					.vip {
						font-size: 22rpx;
						color: #aaa;
						margin-top: 7rpx;

						.vip-money {
							font-size: 24rpx;
							color: #282828;
							font-weight: bold;

							image {
								width: 46rpx;
								height: 21rpx;
								margin-left: 4rpx;
							}
						}
					}
				}
			}
		}
	}
</style>
