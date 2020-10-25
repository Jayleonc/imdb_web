<template>
	<view class="content">
		<!-- 顶部导航组件 -->
		<uni-nav-bar left-icon="contact-filled" left-text=" " right-text="" title="TOP250" shadow="true" right-icon="compose"
		 background-color="#000" color="#fff" @clickLeft="showChouti" @clickRight="gotoAdd">
		</uni-nav-bar>

		<!-- 弹出层组件，有bug，背景透明 -->
		<uni-popup ref="popup" type="center">
			<view style="width: 100%; height: 300rpx;">
			</view>
		</uni-popup>

		<!-- 搜索框组件 -->
		<view>
			<uni-search-bar placeholder="Search in IMDb" :radius="100" 
			@confirm="search" v-model="id">
			</uni-search-bar>
		</view>

		<!-- 轮播图 放弃 -->
		<!-- <view>
			<swiper class="screen-swiper square-dot " 
			:indicator-dots="true" :circular="true" 
			:autoplay="true" interval="3000"
			 duration="500">
				<swiper-item v-for="(item,index) in swiperList" :key="index">
					<image :src="'http://localhost:8080/school_war_exploded/'+item.rotationPicUrl" 
					mode="aspectFill" v-if="item.rotationType=='image'"></image>
				</swiper-item>
			</swiper>
		</view> -->

		<!-- 抽屉组件，显示用户，有bug，每次打开关闭会导致原页面组件重新渲染 -->
		<view id="chouti">
			<uni-drawer :visible="isShowMe" mode="left" @close="closeChouti">
				<view style="padding:30rpx;">
					<view class="mychouti">
						<image class="logo" :src='userAvator' @click="login"></image>
						<view class="text-area">
							<text>{{username}}</text>
						</view>
					</view>
				</view>
			</uni-drawer>
		</view>

		<!-- 折叠面版，用来显示电影 -->
		<uni-collapse accordion="true">
			<movie v-for="(item,index) in movies2" :key="item.id" :number="index" :moviename="item.name" :moviemotto="item.motto"
			 :moviecountry="item.country" :moviedate="item.date" :moviegenre="item.genres" :movierate="item.rating" :movieid="item.id"></movie>
		</uni-collapse>

		<uni-load-more :status="loadStatus" :contentText="refreshMsg"></uni-load-more>

		<!-- 尾部 -->
		<view class="foot">
			<text>© 2019-2020 by jayleonc</text>
		</view>

		<!-- 右下角悬浮按钮组件 -->
		<uni-fab :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction"
		 @trigger="trigger"></uni-fab>

	</view>
</template>

<script>
	import {
		uniNavBar,
		uniSearchBar,
		uniDrawer,
		uniCard,
		uniCollapse,
		uniCollapseItem,
		uniRate,
		uniIcons,
		uniFab,
		uniPopup,
		uniLoadMore
	}
	from "@dcloudio/uni-ui"
	import movie from '../../components/movie.vue'
	import axios from "axios"
	var _self;
	export default {
		components: {
			uniNavBar,
			uniSearchBar,
			uniDrawer,
			uniCard,
			uniCollapse,
			uniCollapseItem,
			uniRate,
			uniIcons,
			uniFab,
			uniPopup,
			movie,
			uniLoadMore
		},
		data() {
			return {
				id:"",
				username: 'uni-app',
				userAvator: "/static/logo.png",
				isShowMe: false,
				isShowGenre: false,
				title: 'Hello',
				isLogin: false,
				movies2: [],
				loadStatus: "noMore",
				refreshMsg: {
					contentdown: "上拉显示更多",
					contentrefresh: "正在加载...",
					contentnomore: "没有更多数据了"
				},
				pattern: {
					color: '#3c3e49',
					selectedColor: '#F5C518',
					backgroundColor: '#ffffff',
					buttonColor: '#F5C518'
				},
				//  星星图标
				content: [{
					iconPath: '/static/icon/rate.png',
					selectedIconPath: '/static/icon/rate-active.png',
					text: 'Rating',
					active: false
				}, { // 日期图标
					iconPath: '/static/icon/date.png',
					selectedIconPath: '/static/icon/date-active.png',
					text: 'Date',
					active: false,
				}],
				horizontal: 'right',
				vertical: 'bottom',
				direction: 'vertical',
				items: [{
						value: 'USA',
						name: '美国'
					},
					{
						value: 'CHN',
						name: '中国',
						checked: 'true'
					},
					{
						value: 'BRA',
						name: '巴西'
					},
					{
						value: 'JPN',
						name: '日本'
					},
					{
						value: 'ENG',
						name: '英国'
					},
					{
						value: 'FRA',
						name: '法国'
					}
				]
			}
		},
		created() {
			_self = this;
			this.update()
		},
		onPullDownRefresh() {
			this.update()
		},
		onLoad() {
			_self.startLogin();
			// this.getSwipers() 轮播图，放弃
		},
		methods: {
			byId(id) {
				var Id = this.id;
				console.log(Id)
				uni.request({
					url: getApp().globalData.baseUrl + '/imdb/movies/findById?id=' + id,
				})
			},
			// 登录页面
			showChouti: function() {
				if (!this.isLogin) this.startLogin()
				this.isShowMe = true;
			},
			closeChouti: function() {
				this.isShowMe = false;
			},
			login: function() {
				if (!this.isLogin) {
					this.isShowMe = false;
					uni.navigateTo({
						url: "../login/login"
					})
				}
			},
			startLogin: function() {
				uni.login({
					provider: 'weixin',
					success: function(loginRes) {
						// 获取用户信息
						uni.getUserInfo({
							provider: 'weixin',
							success: function(infoRes) {
								_self.username = infoRes.userInfo.nickName;
								_self.userAvator = infoRes.userInfo.avatarUrl;
								_self.isLogin = true;
							}
						});
					}
				});
			},
			// 下拉刷新
			update: function() {
				uni.showLoading({
					title: '加载数据中',
					mark: true
				})
				uni.request({
					url: getApp().globalData.baseUrl + '/imdb/movies/findAll',
					success: function(res) {
						_self.movies2 = res.data
						uni.hideLoading()
						uni.stopPullDownRefresh()
					}
				})
			},
			trigger: function(e) {
				console.log("这里被点击了");
				uni.showToast({
					title: "别点我好吗",
				})
			},
			gotoAdd: function() {
				uni.navigateTo({
					url: '../add/add'
				})
			},
			delete: function(i) {
				this.movies2.filter((m) => m.id != i)
			}
		}
	}
</script>

<style lang="scss">
	.home {
		swiper {
			width: 750rpx;
			height: 380rpx;

			image {
				height: 100%;
				width: 100%;
			}
		}
	}

	.foot {
		width: 100%;
		height: 100rpx;
		background-color: #000000;
		box-shadow: 0upx 0upx 60upx 0upx rgba(0, 0, 0, 0.1);
		margin-left: auto;
		margin-right: auto;
		overflow: hidden;
	}

	.foot text {
		font-size: 25rpx;
		color: #555555;
		position: relative;
		top: 25rpx;
		left: 28%;
	}

	.mychouti {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 100rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
		border-radius: 50%;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
	}

	,
	.uni-list-cell {
		justify-content: flex-start
	}
</style>
