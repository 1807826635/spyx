<template>
	<view>
		<view class='my-promotion'>
			<view class="header">
				<view class='name acea-row row-center-wrapper'>
					<view>当前佣金</view>
					<navigator url='/pages/users/user_spread_money/index?type=1' hover-class="none" class='record'>提现记录<text class='iconfont icon-xiangyou'></text></navigator>
				</view>
				<view class='num'>{{userInfo.brokerage_price}}</view>
				<view class='profit acea-row row-between-wrapper'>
					<view class='item'>
						<view>昨日收益</view>
						<view class='money'>{{userInfo.yesterDay}}</view>
					</view>
					<view class='item'>
						<view>累积已提</view>
						<view class='money'>{{userInfo.extractTotalPrice}}</view>
					</view>
				</view>
			</view>
			<!-- #ifdef APP-PLUS || H5 -->
			<navigator url="/pages/users/user_cash/index" hover-class="none" class='bnt bg-color'>立即提现</navigator>
			<!-- #endif -->
			<!-- #ifdef MP -->
			<view @click="openSubscribe('/pages/users/user_cash/index')" class='bnt bg-color'>立即提现</view>
			<!-- #endif -->
			<view class='list acea-row row-between-wrapper'>
				<navigator url='/pages/users/user_spread_code/index' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-erweima'></text>
					<view>推广名片</view>
				</navigator>
				<navigator url='/pages/users/promoter-list/index' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-tongji'></text>
					<view>推广人统计</view>
				</navigator>
				<navigator url='/pages/users/user_spread_money/index?type=2' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-qiandai'></text>
					<view>佣金明细</view>
				</navigator>
				<navigator url='/pages/users/promoter-order/index' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-dingdan'></text>
					<view>推广人订单</view>
				</navigator>
				<navigator url='/pages/users/promoter_rank/index' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-paihang1'></text>
					<view>推广人排行</view>
				</navigator>
				<navigator url='/pages/users/commission_rank/index' hover-class="none" class='item acea-row row-center-wrapper row-column'>
					<text class='iconfont icon-paihang'></text>
					<view>佣金排行</view>
				</navigator>
			</view>
		</view>
		<!-- #ifdef MP -->
		<!-- <authorize @onLoadFun="onLoadFun" :isAuto="isAuto" :isShowAuth="isShowAuth" @authColse="authColse"></authorize> -->
		<!-- #endif -->
		<home></home>
	</view>
</template>

<script>
	import { getUserInfo } from '@/api/user.js';
	import { openExtrctSubscribe } from '@/utils/SubscribeMessage.js';
	import {
		toLogin
	} from '@/libs/login.js';
	import {
		mapGetters
	} from "vuex";
	// #ifdef MP
	import authorize from '@/components/Authorize';
	// #endif
	import home from '@/components/home';
	export default {
		components: {
			// #ifdef MP
			authorize,
			// #endif
			home
		},
		data() {
			return {
				userInfo: [],
				yesterdayPrice: 0.00,
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
			onLoadFun: function() {
				this.getUserInfo();
			},
			// 授权关闭
			authColse: function(e) {
				this.isShowAuth = e
			},
			openSubscribe: function(page) {
				uni.showLoading({
					title: '正在加载',
				})
				console.log('666');
				openExtrctSubscribe().then(res => {
					console.log('123')
					uni.hideLoading();
					uni.navigateTo({
						url: page,
					});
				}).catch(() => {
					uni.hideLoading();
				});
			},
			/**
			 * 获取个人用户信息
			 */
			getUserInfo: function() {
				let that = this;
				getUserInfo().then(res => {
					that.$set(that,'userInfo',res.data);
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.my-promotion .header {
		background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAu4AAAF3CAMAAADNQj7uAAAAYFBMVEUAAABLVqpLVqpRXK1LVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpLVqpSXa1OWaxLVqpLVqpLVqpRXK1NWKtQW61PWawFX8UfAAAAG3RSTlMAzN7M+8QLBdqaaOh3QBX0i4FaI06sLczMtG7kNZeKAAAcuElEQVR42uzSgQAAAAwEoXnM3/NBrhi6hwzdCdGdEN0J0Z0Q3QnRnRDdCdGdEN0J0Z0Q3QnRnRDdCdGdEN0J0Z0Q3QnRnRDdCdGdsWdGyQ2DQAw9CCvp/rfsZGqK68XgJu7MAtZXEvIRSY+1TRbSe7gDSI+eILIwSg5/xB0Ss6RBPP6HIO6CWDYHYCweruO+sW47kWsWDZH2BOF4iL/zr+MO0SoaYEvfLMgqWg54iOPlcBH3Ys4rtL/reoL4k3AaAwPHcAn3zdyI/u6WniBSyle48fb9Jdwhmo3p715B1pbSEgLNbMR938d9M/fw/h3Ew3tKMrMxee/i7kqmpHH83SgfBLQk73I8cBQe+rgfO8XrMy5YM+gKXZJ3eb8Yhfc+7qrZOKt54lPJY6VMqdHzvHd3x+oLD7V8guXQxR31NmlOrwXEPnb9RKpO8bP7OsWcbp8LrPEAmpMC8tDDHbTGdPe8K+hV7GP5IFIJorLCOe9q4AwX3Ku8x+KhgztoVdxx9jgOxvJ3l0Br4u57nvMuXt5siaeGSiweOrjLrLmb/ZKiPqV8JlVneGPfJ83IO9jC3S9Fm38t3Ksu6HCnxL29GU/hwT7upLTvecZTmtOTKHke8niPNP/auMu6051ASmJZ0yI1H3EXkKCyhlA93yOwizuF8j3lHFIQtXFnD/f84K1sb52atyB+Wt6+WNYQ9vT5bcmauBfD2L1joPnXxB0d3EubKvYQ90+GtwXr4K7sl7sgpvvTCT3cKfwOTHkpCA9N3A/uyPLD+atLcGePZoEuX2+qHwQL7oQLAq/Xs/H+xd4ZJKkOw0CUeyjdff9b/voVMziR7RgmTAmFXkzVABu1nuTYcWLQ21DjTmwMKxgoUP8b4r6NToBY4c5HJrWdi8cJ7xyBu8n6wwjQ0f6YxWSre21pB8AKd8rRXiYxcdr7CHfQ3xQG77jLJblQjnTtHfQrb2LBvYJd3PwI2fbQqLVEV3Dv8hCqvc/jrvJhmZu6JNe4xynnU4TW3iesf2sjdr/CkqzuHe4PHiCPzZ0axSn7Ee5if4ubS3IVXjrcfYhegHMLgbcGviIXYUvYUWOfg/vxlk6Ii8c9dZrbd9FAbxaS7Qj2+/3bPDjc4/AwjTv9WweKAQ3cc7X3XVdrJBpoLlMy19WM98HxoNbNOMUp+xHurDEGyG2Jdm8pKzHuFETuOjzaW4uQrO63cxPIAd/cahFqlJvs7oQ1VhjFJu6ME94pAt2GqDq8Hu7phjlwGx7EmofuFivFeb5piDvdssxuOztEh3uowescVTSjBM5FNjQClq7uK5rZ4QFqdfdPxd0/nQd53BknvHPkcTccTGN4Cdw9DybucP+Y7g72VmXqMLXHHZlx90ZYUXMhMtVctY5Q6PEAP9rHeWh9Dne/KsN6N5C7zZTrmtXQX34DeTdGzojQD+W/INYBbnnQgwd+KO7G/ro7S+LdToOEWTb015vBkvlWmrMZob4P+un48gtUH4K7lo38LFyAGmNXtu5u3PlQGQGtlQ86I5gNd+x5kO18wM4HfjLu28SBixMsYVPbGuEzJ3aeUk927d7IuLv7QO8TAr1w6rnHO3TwPWEJm9qkEZG72kmSD7MW3PfRrulGuB/vmoEr5pRZNuOTRsBidbWTBNfcd1Lw9jfGHWz2NOixGuHCV74suzh190HFBz+rTzmHgQtz64MHJtg13RD35tbXNQABne+ZL8u9va8oyxPtNEfqamdJ7h5SSTlbPDBc+zvAHS3cVT3D5ofw2K+AfVVqDeJcXSlGuDQH6mrnyJV9DQmFdtV/EO6mwSuVyMb+CGbMsl9Vrz8juQRP82mC4+HBCNm6pAvV/o5wR69teXFt7hmvZZwRwuYTR3u0NJ8nPcNDuKp/8oXXpLh0tEaXNMtmmjWCsLyj3L7/kVyGVR+r/R3h7ot3HF3iQyw4bUTqsofzYVT1wU7zOMR9lnclb+6zeWb6stcTtDNWc5/BHZyPLnGWJ41Yac9c9qZpHrQEu6S72bGgw+AK7cGGrrN1zHvZJZr8YDJxcowLx8PNZqSj4C5B+3HhE3YJIzB1rLTiVf15h8QjYHTnS/zSbkfnqGu1KqAPN5sTwGFrN/AKtI8STVgxItj87C0CD05QV8QzVmdx7+WZuif5MifGD42ALlL2BogDHkLS/gTua4CsQ6N+gmPI6N4kSFw6Rlzp/OwdD0t8Hm72lAD+SMD9Uy6X6e1FUMuIOsvBDtB9j1D5IOHHnKinxd/sBQGb/7RcqaV1jUDcLDu91YfA3e9mvxS4XDXJW4nLxVp7U9CGh2Dd7xe4u2tYXhZ26At7sYGVDfF8eBl3iOTyzXExInSS/0TwPMTz4XXcIT4yHDK2vxHErxGrD3XJx/ThZq8LgP4LCBnau+WN+PoQnYebffXVZfTF/R97d2wCIBAFQbSS679N4QINzETlw7xXwjL5EiJ3QuROiNwJkTshcidE7oTInRC5EyJ3QuROiNwJkTshcidE7oTInRC5EyJ3QuROiNwJkTshcidE7oTInRC5EyJ3QuROiNwJkTshcidE7oTInRC5EyJ3QuROiNwJkTshcidE7oRMzX1tw09p/3DtkB7ilR6G5r728fqp+7y+n/gvU6/Xv7ZuOzybYWbuB3tntyQ7CALhBwnd/f5veapyZkMUEmd/LgyON1s18YbmAxVMVrQwqG25AVkcXI93pDz8QIcJcX/F8Qf4/7B/gActH98Hfj7cd+M+fvYlLhnUOkL8KQ+z4X6k9tUTPGRmn8CH0/4XCX4y3EEbjEV4F80+vDsPf6TDXLh31pFclHdZL8SSC13kgb/jfSrce+sAaEne0VkMYMXAhwIP+B3vM+EOJu5MeN/Nq1x+FhN/Jn4uLkTKAywMvS/DTLgz9SZz3lF384pUCCW81xZCKda84J3axmMi3GU3uMcnqrp5RS6ELA7uAhUVArzFPea/N+J+HtyjHY57xjvMalafZXncX9WpUHMXHx2v+0Yzbcz7NLiDdot7tFw1eQcHuAeNVJL3iLX893yZG/M+De6yN3Anz5azopvxfSFgZuXiHnwDd5Ln7a0NeZ8F9/RE2h1gKUCnZ6pYjMNlBaYV4uxnFuw6yYa4UwDozzDOf7PgLrsyj+09EfkzLOJmz+4J/fgSYis10gpF25PQfx1OzzRc5h6D+xfXaLLaEm5ucRdeirkQYLl1TnaFu7qmMh0W2Yj3SXAH7X7xgutwNq8c7znu7lemQrBck9Vxz4/sggvmYT9ssk6Cu1q7BPGMO+E4tGfxaqe0TgiIjntLOyvHfRf1gnjGnYqCYdswDPsZcd8DFTpwp1rafVK9rMYoBA/cKcR70toKCoHYXRAP3E88qC3JjtL7rLj7LQgxGuelp2LpHUzugL28m0S9x32xO8E5D9j/UD6NfStmEPZz4I6Q1HwA1yyo2s3AYGImBEDrcS8W93qLhzALNtjVzYh7zi8UJ6namw6ynwnBWnEPjq/4QnESRjxMibsRwynGnY7KWc2FCFMC7rXiPvAgpFMi7oOwnwN3NaaJwUCIluFe7T2HToiAcNpbrxf3jZUc8+ClmcFuZj7ctW1AbyBoq+FObMAOvMa9ddYSoj2KD3lw3EfvN82BO+jWefSiiWZmuFd7TVl0ig8hiNu0pnpxD4Z319RaBzHN7s/EfdsgoZuzIu67EPefJlE9IaCG4n0EHpThPgj7+XDH9aTqXg5x/7YQLCZEwD0bSo6qT8PdqD7ScdlqU7V38sHrqowSIbzuXuusKvMhJDzkLWiNUsUcuG+6LjvpMDjprlTDPXrQBw9hFHCvlt2R8OCPiLQn91DcWwMhM758uhjunRB0IYKbi2V33NTdj7qk4tb9Ibgjv+zpwBuzpmq9Dwwpqbe2ka9MiGpf0ouFxr46RanV6VG4M+AeqxH1vZw2j++FIEoKoejvcdcRfMZRNZqngMESXo4fC8M7QrCcEIhRPQYG9hTckVsHwCcE6+p52d0YhPAJUYh6mzowT3/OA7P094yuavAij2MJhQR3oubWPS2qt99QURSC1U6qiZmHDsKVTuBzcEfq5eNYDsXnrIj7pishSCG4GVVXOTDDnbsObnRIf4+4IhbczOY3Cow7WlX0clZm9MimlK3hJf/FQSgzujaUlCf3Z1wATtzcRnC2k2NJL4cWS+/68BQ1V7nW6JDR86h/xusd2XYGuLcOZT/r37eYoSshXrFQcOse4KYAmY88Fp7x8l7j5vHYraua3P+xdwbJDcMwDHyIBfD/v+xM7UlqyVHoTA+hgD12eim4pHoxeXW54H2ZV9u8cb0ENtf137+JoGvnKVx6pt0JYvW2j6TtePxukT0zO2C+lxeu8h5E0vZoqw73rO/RJVZgi9gBmLV98YNc+SDWvjwZt9+4FnV0fw6r+b1ocG3bE74TCm3//r5s7wMrrETN/n3E7sLaMy3R+HEEsfK/Mom+J05ZlVl4nToTz5CxfV5oHuszFYKY+dBPvzLnDP4S5ET2DVz9BR8KfR1EiJyMxwsfOPrAbc436n5VaD5kD6m70XwdhIrtl3tXSGJYmFnoFFkHEOcSY5j8ax7em3Y+CVz8eNHDqn0OnQ/DPKh0aHIEABAATjtfdUbaNAjqBYEn3VGH/Fv/xboPgIJFfvW0a71x8xzyPlTRHeheddUin3Noqjngsxwq6A5084yhWWQggk2+6Qcf8jkU0P23jT3Q9iAs+7Zh8CGdQwHd9wf8IAKaNe6D0M7hUx9K6G7MBOtujHU38lh3I4R1N0JYdyOEdTdCWHcjhHU3Qlh3I4R1N0JYdyOEdTdCWHcjhHU3Qlh3I4R1N0JYdyOEdTdCfKA7AED5W0kHcRlDhSDu6Q4EG9ka2Sj7LXwXhPKH0gjuhxGL+HBHd5BszdsfthiDUFjU2IMo50Ned7AdaO99QRxBFKv0f4NgPR9yuo+yy65r7IMQXdc4yF5jI3FK96PIrvO+hNiNX9aHhO5HkV3nH/bOILttGAaiBwln5v63bF+qGhIAknWrBQQXq0TKIpj5gCmSomcuf9I3LBw88KE87HH/ZfJ/3n+GfNZPamy3ReSBj+Fhi3t6BuVz8rsz4MgW9JFCKPLwlAH8HnefC+xasPkRSw1/G2BMGCOEmusAJfk+hYc97szS0MzmmkV9R4DZMJ0TIdRWCKVYT3hHscHdFneNFPeJzag97fovoZCuXY2ff+oqBLjG3X3tI2sN7na4gznuY2Zz18Grgpt2OZOIPYWIXBvuaf+rNVu1wR2cjMnGxGZ8jM0Od38LPZ9aNRnEctr/SvG+wV1pDvP0utoMpUwv6v5LHYUAd7jH4W2lWZoV7mkW9LhTAk/32NFmcY87BZ19brjqZLYvnmEosGrZr3HX2HZ34ftXu6cPsdnhTh3JX4Qo4/MdscPdEAftHir1vyXu4Bp3y0Pn9PrxvhDCrS6dfe636KSxxN32Q+KkSiUe/g33I7lrVxujXVvTWONus440n9FukRVc42484HRThTYTLXD3LlPkC2SeF4pNBx23Br86Bb0QV9wJE8JqASzk8y2BoMMVd+NBpxGfCvW/Je70ZQwZ7sFkG8307mrEF0TDnfJCHO6qW3uX3w0H0XBnpH2wWNmvcAfjhxZ04H6yURyX9Nq194UQIA12uTm4bnuClUzR6TfuQiIXi5X9e7hbKJpsuBf69Lol4G22QHTZhBi96h5c6HDigY4a1Sn7Fe7a7my15M7psU56b8RtQhjuzXYEI1odA7zq8PVQ3NOXbsExIu69bU4zE+O8DZq94eR1yHgQxwi41+HhT3En6YG35ALuvUYzoBdC8EIk05Tt6h7jFCkP2c6iUmW/wp2XSoZcgtNd72qMO3EIAfg/iN29Wd1rxwM4Hoy73Io55NaDQaa410nvlgDDSyy8picmuLf7mAPDYiMv2UHpBlrVea1xiTvdY0l8awEKuJdK756Qs/nbWF10ADPce9U9aBS/LtHxwKS71+FhOxHpHfP/syLurJPeHeFwPwJwYsXpG/THPfIAOtxLtb897vlTuLkt/wI6OuPOuRDZRGSrmfczD5zKgPhpX+cQnj3uuc98FQCSptZrzOpTxPmWCaMgRLezODTnQYP6Tc1Dcbd+FRPUOKbjwDhUa4c7xwx46BAms7kz7o4H8LiQVD0fgrsWZ+X8no7TaO9yPF6DcEIIYHxS7SYEAg9wPAgaz8U9sdkC6fRbS9y1PAsSZP6WerOxe3YMQcAl6oRCB2i+8XpH/Idj+2/Z1KIQxFaoWl3tptB4TwdWa3+7d1Xz7Gxa5iNcthJ2qeElCH3X6/gME0YzOx749VWr/b11EkFcbFJSzP1cTsr6O2BCMNgM9hMCTNsfqNdPtdvf7pyZ1GV7SvH3e7o82+qtlxBgbO4dzwSGr+rvYOTBMlehofsOd2S463XQsT7EZe/jpaUTgDwGxWy+K8AEd5x4KN7+DPc8tDhSicxG7h1d9nsErJVlQrCxEODizKH0QbVU+3vvSFQC7ko88LxSMd8UUQilQhjf1Wy+L+Qn3qE5D+WqfoN7eBgnucmu22TzVIjxIJtvDAYellVfq/3tcI9zrevsGn8plxNiQ3vT5m6p7XUoyEPAPYbeob1tc7fh+97lejbfGeAbVV+ruRvui9Af087GLjuf10L0be5OhzXt5Xj4E9z3vFM1s7s5sBfiF+3Nv2j1B3tnlNw2DAPR3sPc3fvfsh3VCSySspTY7SDEvq92ko9g8QDTsjwCL9qe7nmEofszwNPiUlb3dnQeRIGxP/Vdm+3K58PVh8SL503O/lDNdwCeHGRqjP3tgg8ZX+Mu6j72uW8pmLC693MehGo8Uhh6erC9gRlzuKz7fMOTuI9yFdtvN4A8avINRWyf+RA5ZLU9dL8C9hVSwr24RwEWfaBol8M+CNxrFtM/J/1tjD7w0wfltD10vwgkiZSk0PqxuFUfn9sBSNI9B3yG03J2+d8BzXJgS7r9ft1eBQWbPAWgg7iv/Kwn21d1h9jKrfYZUGuVDjJT+plP9oj413QHRMv+B2g/9FWDyO/DN3Uf35dT+Yr7L4B9ECgZxOZDbtlf2e4Qya0ukkLNHj8GQZJVXf8DIIYPSXN46eyOvxRW/S9wED8kh183Y8pg3U0hrLsphHU3hbDuphDW3RTCuptCWHdTCOtuCmHdTSGsuymEdTeFsO6mENbdFMK6m0JYd1MI624KYd1NIay7KYR1N4Ww7qYQ1t0UwrqbQlh3Uwjrbgph3U0hrLsphHU3hbDuphDW3RTCuptCWHdTCOtuCmHdTSGsuymEdTeFsO6mENbdFMK6m0JYd1OIrLpDn1R9+vwGIoisT6KekTWHlLpDYnuARY1HH0TRZ9ADIlvAb+aQUneojbCe8AAdxObDNIdvCJ9Qd6gdUKzPYJtD3QoBHebwZR/S6Q61Y1jIdwcRQ3/IV+c+m+4fTS6/4MH2lCILPlb7W+Y+me5ia/Z9N/WlDzQx9O/xPZfu2ldCiTVfx4cgWHLwO9tf9yGV7uDQT0zquy2PBrGhgucZcLgUg9d8z6R7X8pWxlGbV778rFk/Z31eOwhw7Puh77iUQybdOe0m28BW3rqHV0yD0FGflz3d9VM/0z3mgLqQQyLd1TpC99lPtOopHhytjnjGc50WPdUMOYTuM9/BCz7k0V3tku4hANf0HTo4tLSjPq95ih8br/DkYP+d5pBGd/Ci7tHmNa/SgAe687DPS/qu9gXduQV07kMa3dUG0J/oSfFxva/oO9jOdSf5OPcrXqUBL+i+90HnPmTRHWwH5XF3M6AYP9OKbVY71Z0CoNgJW0SrzT3bALuAKGypxHo/9T2L7monun8UoigPK7ZZbQShewTBCAnrvc7hUHeE7A//JS7tv5+iO4V7Dg9Xpbjeh6zgc90jCEWfweU+fVMbYV93P/ZqZ/svie59l9md1YjIIWrXeh+yqsuhfxNDRGLRZy73IevMB8Y6j2LRQn/c/3FMEt21Lw4QQ/fHEsD9e/HVjjP9bRQQH3SndmMfa2211znsfRDA0H304a4BTsc+o+5bMRA/dCcw3g7KrbzV7hkDZ0F86B6yQ/s7i1Zb7xgvO4MfulOY5IXxJDCSUfd72+6Sa1JcHNYWW+/gl4KIuV8siGH9RQzgMPQxExHbnBy6j0stmKz20H21OwOfBnE7DmJt3bXP6DgsRGxzMuo+/3tBtkH31e6AV9shXLxRkGvN/SUfxLaG7rM+a3ZZSm3lrfYHCpduJeFaQQxFEpd+BZHInBy6a/zKijAWN+q+2vccxiB4GoTWm3twN/EahMf8nhPwZL3n0x0f5RKldd+2FXrh1aa6c60gwH150ODDXPf2I3QH+0+Kof2ugjjTfbWvr4rdZRmA+z6PQWi9uQd3Ft+FV+fD8XY/zCGp7pNvY0EVdY8cnr9VXSuI3+2d23LiMAxADSWQhju0bFtH0v//5XZpuyqJTJykO2vFOi9loC9ijo3kiwLU0F3yQS5VryjSHcIuhCY1JP9z7MvZ/+Pl4dfDlfe/syAPN7z8eecXv76h3HuNkLRq0V6I1K97Kz5CCG61Eb8ez754rLbnlZsYq/O2eiwWXhUUXpUBrmWw9WvPr2XS0N1TePWN+OwvNgcz/pTui+K4nZzoN9Jvj5qUB2kZkj8iELagtepe1/g9QEI+yf9vdC+qncuAXVV4JUCwZxov2wG2K1Uluof2FbjvM8qbquN1Xx/OLhvOh7XXgLSZxkg+4GR09x7k8In/eSjzaumyYvmmQnixCwsDJPfe0FGqcnih+RpIar0xVvd1NemEXWalQXiQfWBIbMmgRffAoSAg4PiDgxn9IPbHzGb2L3aH9KtW9kFefQeUOm/o2FVtL6rzuwRirT4+dS8yytmbXJIvWuW1GcIaQdKdlOkOku7AVQgJ0Y3Rff/msmaT+gSPku7IPqDQPbDWcSIydIGFuJ2SNLmPSN3Li8ucS+mTBrCdzAD7IOW2mnSXrmnefXLFmMn9OdOs/TvLZ5801PYBaxn6mv50XO+QfCeiYHS33wb4vhyd8c7Bp0y8DzwWdNxV5eEsIkc3OJdZnJxxZeNTBrCOg1MZJZ0IPqAhtte9bX9yxienpAtWiPOB2B0lfWb6DGdoNJzxfTDbb3lK2neqIyBWR0sXsU+AosfysFTGMpkmJ58yEb4j/w50T+5p6S7E1/XgFvL9qJwxofwdqZHaooaWqLHxEXgpungOzmjw6FMGqHP2Yx/U9HdngPB+cJ6G215keCSsi1XiBwog7APPfpE/9unpzsIHgqN6sO3rLK5x9GWX+AnJOz70tT1F3a8BIt6EhkjgW7aT78fWGQJPPnWoaTz78P0TPU/eCxmPH6oDD/ShtlvirjR9vwKALAQByP3GfAfJ6v4O/EVM4xB8P0o7KBNgOfca+HABAOQ6VtdTszshFDK3Htj+kuJ0RgCw9+ynRXegsbL7Z2cEefW6ACDE/j5o0B2IQ+MypS+LjC8vdXNJ+jBBywdq+hAphArdOTauW3vz6Azd1eoXMFx2Dbp/jmekdwa6bpN7F2dF07sHIKIhPujQfTQ2uXeS+N2mMKa7QPZ3U7u4+CzIQ/fCGR3MfA7kobudcu+k8jmQhe5721DtZKmpWDXdw9gWkxWrWelu5wfU3+Mz3aMpnRFB4n3FTPdINs5Qf23VdI9kb5eYotjpfEyf6f4d65EXzdFPnunrPrdVyGld8zDd77CwG6rRbCe/9j553S2VsXQmH93tKORUz72b7oLt1kipF6uJ+z5p3Re24j69pzaZ7gFKq1IHsJ3y7up0dV9vLJEZxGqTeBs9071Fkdvj33+SZZV4n9RcdZ+3KWevh5OdGxjJ7nR4nZXzFsp3on4Dg57Dd3v7aT8AAAAASUVORK5CYII=");
		background-repeat: no-repeat;
		background-size: 100% 100%;
		width: 100%;
		height: 375rpx;
	}

	.my-promotion .header .name {
		font-size: 30rpx;
		color: #fff;
		padding-top: 57rpx;
		position: relative;
	}

	.my-promotion .header .name .record {
		font-size: 26rpx;
		color: rgba(255, 255, 255, 0.8);
		position: absolute;
		right: 20rpx;
	}

	.my-promotion .header .name .record .iconfont {
		font-size: 25rpx;
		margin-left: 10rpx;
		vertical-align: 2rpx;
	}

	.my-promotion .header .num {
		text-align: center;
		color: #fff;
		margin-top: 28rpx;
		font-size: 90rpx;
		font-family: 'Guildford Pro';
	}

	.my-promotion .header .profit {
		padding: 0 20rpx;
		margin-top: 35rpx;
		font-size: 24rpx;
		color: rgba(255, 255, 255, 0.8);
	}

	.my-promotion .header .profit .item {
		min-width: 200rpx;
		text-align: center;
	}

	.my-promotion .header .profit .item .money {
		font-size: 34rpx;
		color: #fff;
		margin-top: 5rpx;
	}

	.my-promotion .bnt {
		font-size: 28rpx;
		color: #fff;
		width: 258rpx;
		height: 68rpx;
		border-radius: 50rpx;
		text-align: center;
		line-height: 68rpx;
		margin: -32rpx auto 0 auto;
	}

	.my-promotion .list {
		padding: 0 20rpx 50rpx 20rpx;
		margin-top: 10rpx;
	}

	.my-promotion .list .item {
		width: 345rpx;
		height: 240rpx;
		border-radius: 20rpx;
		background-color: #fff;
		margin-top: 20rpx;
		font-size: 30rpx;
		color: #666;
	}

	.my-promotion .list .item .iconfont {
		font-size: 70rpx;
		background-image: linear-gradient(to right, #4B56AA 0%, #5F51B3 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		margin-bottom: 20rpx;
	}
</style>
