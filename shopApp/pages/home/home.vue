<template>
	<view class="">
		<uni-search-bar @confirm="search" :focus="true" v-model="searchValue" @blur="blur" @focus="focus" @input="input"
			@cancel="cancel" @clear="clear">
		</uni-search-bar>
		<view class="search-result">
			<text class="search-result-text">当前输入为：{{ searchValue }}</text>
		</view>
		<uni-swiper-dot class="uni-swiper-dot-box" @clickItem=clickItem :info="info" :current="current" :mode="mode"
			:dots-styles="dotsStyles" field="content">
			<swiper class="swiper-box" @change="change" :current="swiperDotIndex">
				<swiper-item v-for="(item, index) in 3" :key="index">
					<view class="swiper-item" :class="'swiper-item' + index">
						<text style="color: #fff; font-size: 32px;">{{index+1}}</text>
					</view>
				</swiper-item>
			</swiper>
		</uni-swiper-dot>
		<view class="tabs">
			<scroll-view ref="tabbar1" id="tab-bar" class="tab-bar" :scroll="false" :scroll-x="true" :show-scrollbar="false"
				:scroll-into-view="scrollInto">
				<view style="flex-direction: column;">
					<view style="flex-direction: row;">
						<view class="uni-tab-item" v-for="(tab,index) in tabList" :key="tab.id" :id="tab.id"
							:ref="'tabitem'+index" :data-id="index" :data-current="index" @click="ontabtap">
							<text class="uni-tab-item-title"
								:class="tabIndex==index ? 'uni-tab-item-title-active' : ''">{{tab.name}}</text>
						</view>
					</view>
					<view class="scroll-view-indicator">
						<view ref="underline" class="scroll-view-underline" :class="isTap ? 'scroll-view-animation':''"
							:style="{left: indicatorLineLeft + 'px', width: indicatorLineWidth + 'px'}"></view>
					</view>
				</view>
			</scroll-view>
			<view class="tab-bar-line"></view>
			<swiper class="tab-view" ref="swiper1" id="tab-bar-view" :current="tabIndex" :duration="300"
				@change="onswiperchange" @transition="onswiperscroll" @animationfinish="animationfinish"
				@onAnimationEnd="animationfinish">
				<swiper-item class="swiper-item" v-for="(page, index) in tabList" :key="index">
					<swiper-page class="swiper-page" :pid="page.pageid" ref="page"></swiper-page>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>

<script>
	// #ifdef APP-PLUS
	const dom = weex.requireModule('dom');
	// #endif

	// 缓存每页最多
	const MAX_CACHE_DATA = 100;

	// 缓存页签数量
	const MAX_CACHE_PAGE = 3;
	const TAB_PRELOAD_OFFSET = 1;

	import swiperPage from './nvue-swiper-page.nvue';

	export default {
		components: {
			swiperPage
		},
		data() {
			return {
				searchValue: '123123',
				tabList: [],
				tabIndex: 0,
				cacheTab: [],
				scrollInto: "",
				indicatorLineLeft: 0,
				indicatorLineWidth: 0,
				isTap: false,
				showTitleView: true,
				pageId: "page",
				refreshing: false,
				refreshText: "",
				refreshFlag: false,
				info: [{
					colorClass: 'uni-bg-red',
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/094a9dc0-50c0-11eb-b680-7980c8a877b8.jpg',
					content: '内容 A'
				},
				{
					colorClass: 'uni-bg-green',
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/094a9dc0-50c0-11eb-b680-7980c8a877b8.jpg',
					content: '内容 B'
				},
				{
					colorClass: 'uni-bg-blue',
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/094a9dc0-50c0-11eb-b680-7980c8a877b8.jpg',
					content: '内容 C'
				}
			],
			dotStyle: [{
				backgroundColor: 'rgba(0, 0, 0, .3)',
				border: '1px rgba(0, 0, 0, .3) solid',
				color: '#fff',
				selectedBackgroundColor: 'rgba(0, 0, 0, .9)',
				selectedBorder: '1px rgba(0, 0, 0, .9) solid'
			},
			{
				backgroundColor: 'rgba(255, 90, 95,0.3)',
				border: '1px rgba(255, 90, 95,0.3) solid',
				color: '#fff',
				selectedBackgroundColor: 'rgba(255, 90, 95,0.9)',
				selectedBorder: '1px rgba(255, 90, 95,0.9) solid'
			},
			{
				backgroundColor: 'rgba(83, 200, 249,0.3)',
				border: '1px rgba(83, 200, 249,0.3) solid',
				color: '#fff',
				selectedBackgroundColor: 'rgba(83, 200, 249,0.9)',
				selectedBorder: '1px rgba(83, 200, 249,0.9) solid'
			}
		],
		modeIndex: -1,
		styleIndex: -1,
		current: 0,
		mode: 'default',
		dotsStyles: {},
		swiperDotIndex: 0
			}
		},
		onLoad() {
			for (var i = 0; i < 6; i++) {
				this.tabList.push({
					id: "tab" + i,
					name: 'Tab ' + (i + 1),
					pageid: i + 1
				})
			}
		},
		onReady() {
			this._lastTabIndex = 0;
			this.swiperWidth = 0;
			this.tabbarWidth = 0;
			this.tabListSize = {};
			this._touchTabIndex = 0;

			this.pageList = this.$refs.page;
			this.selectorQuery();
		},
		methods: {
			search(res) {
				uni.showToast({
					title: '搜索：' + res.value,
					icon: 'none'
				})
			},
			input(res) {
				console.log('----input:', res)
			},
			clear(res) {
				uni.showToast({
					title: 'clear事件，清除值为：' + res.value,
					icon: 'none'
				})
			},
			blur(res) {
				uni.showToast({
					title: 'blur事件，输入值为：' + res.value,
					icon: 'none'
				})
			},
			focus(e) {
				uni.showToast({
					title: 'focus事件，输出值为：' + e.value,
					icon: 'none'
				})
			},
			cancel(res) {
				uni.showToast({
					title: '点击取消，输入值为：' + res.value,
					icon: 'none'
				})
			},
			ontabtap(e) {
				let index = e.target.dataset.current || e.currentTarget.dataset.current;
				//let offsetIndex = this._touchTabIndex = Math.abs(index - this._lastTabIndex) > 1;

				// #ifdef APP-PLUS || H5 || MP-WEIXIN || MP-QQ
				this.isTap = true;
				var currentSize = this.tabListSize[index];
				this.updateIndicator(currentSize.left, currentSize.width);
				this._touchTabIndex = index;
				// #endif

				this.switchTab(index);
			},
			onswiperchange(e) {
				// 注意：百度小程序会触发2次

				// #ifndef APP-PLUS || H5 || MP-WEIXIN || MP-QQ
				let index = e.target.current || e.detail.current;
				this.switchTab(index);
				// #endif
			},
			onswiperscroll(e) {
				if (this.isTap) {
					return;
				}

				var offsetX = e.detail.dx;
				var preloadIndex = this._lastTabIndex;
				if (offsetX > TAB_PRELOAD_OFFSET) {
					preloadIndex++;
				} else if (offsetX < -TAB_PRELOAD_OFFSET) {
					preloadIndex--;
				}
				if (preloadIndex === this._lastTabIndex || preloadIndex < 0 || preloadIndex > this.pageList.length - 1) {
					return;
				}
				if (this.pageList[preloadIndex].dataList.length === 0) {
					this.loadTabData(preloadIndex);
				}

				// #ifdef APP-PLUS || H5 || MP-WEIXIN || MP-QQ
				var percentage = Math.abs(this.swiperWidth / offsetX);
				var currentSize = this.tabListSize[this._lastTabIndex];
				var preloadSize = this.tabListSize[preloadIndex];
				var lineL = currentSize.left + (preloadSize.left - currentSize.left) / percentage;
				var lineW = currentSize.width + (preloadSize.width - currentSize.width) / percentage;
				this.updateIndicator(lineL, lineW);
				// #endif
			},
			animationfinish(e) {
				// #ifdef APP-PLUS || H5 || MP-WEIXIN || MP-QQ
				let index = e.detail.current;
				if (this._touchTabIndex === index) {
					this.isTap = false;
				}
				this._lastTabIndex = index;
				this.switchTab(index);
				this.updateIndicator(this.tabListSize[index].left, this.tabListSize[index].width);
				// #endif
			},
			selectorQuery() {
				// #ifdef APP-NVUE
				// 查询 tabbar 宽度
				uni.createSelectorQuery().in(this).select('#tab-bar').boundingClientRect().exec(rect => {
					this.tabbarWidth = rect[0].width;
				});
				// 查询 tabview 宽度
				uni.createSelectorQuery().in(this).select('#tab-bar-view').boundingClientRect().exec(rect => {
					this.swiperWidth = rect[0].width;
				});

				// 因 nvue 暂不支持 class 查询
				var queryTabSize = uni.createSelectorQuery().in(this);
				for (var i = 0; i < this.tabList.length; i++) {
					queryTabSize.select('#' + this.tabList[i].id).boundingClientRect();
				}
				queryTabSize.exec(rects => {
					rects.forEach((rect) => {
						this.tabListSize[rect.dataset.id] = rect;
					})
					this.updateIndicator(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex]
						.width);
					this.switchTab(this.tabIndex);
				});
				// #endif

				// #ifdef MP-WEIXIN || H5 || MP-QQ
				uni.createSelectorQuery().in(this).select('.tab-view').fields({
					dataset: true,
					size: true,
				}, (res) => {
					this.swiperWidth = res.width;
				}).exec();
				uni.createSelectorQuery().in(this).selectAll('.uni-tab-item').boundingClientRect((rects) => {
					rects.forEach((rect) => {
						this.tabListSize[rect.dataset.id] = rect;
					})
					this.updateIndicator(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex]
						.width);
				}).exec();
				// #endif
			},
			updateIndicator(left, width) {
				this.indicatorLineLeft = left;
				this.indicatorLineWidth = width;
			},
			switchTab(index) {
				if (this.pageList[index].dataList.length === 0) {
					this.loadTabData(index);
				}

				if (this.tabIndex === index) {
					return;
				}

				// 缓存 tabId
				if (this.pageList[this.tabIndex].dataList.length > MAX_CACHE_DATA) {
					let isExist = this.cacheTab.indexOf(this.tabIndex);
					if (isExist < 0) {
						this.cacheTab.push(this.tabIndex);
					}
				}

				this.tabIndex = index;

				// #ifdef APP-NVUE
				this.scrollTabTo(index);
				// #endif
				// #ifndef APP-NVUE
				this.scrollInto = this.tabList[index].id;
				// #endif

				// 释放 tabId
				if (this.cacheTab.length > MAX_CACHE_PAGE) {
					let cacheIndex = this.cacheTab[0];
					this.clearTabData(cacheIndex);
					this.cacheTab.splice(0, 1);
				}
			},
			scrollTabTo(index) {
				const el = this.$refs['tabitem' + index][0];
				let offset = 0;
				// TODO fix ios offset
				if (index > 0) {
					offset = this.tabbarWidth / 2 - this.tabListSize[index].width / 2;
					if (this.tabListSize[index].right < this.tabbarWidth / 2) {
						offset = this.tabListSize[0].width;
					}
				}
				dom.scrollToElement(el, {
					offset: -offset
				});
			},
			loadTabData(index) {
				this.pageList[index].loadData();
			},
			clearTabData(index) {
				this.pageList[index].clear();
			},
			onrefresh(e) {
				this.refreshing = true;
				this.refreshText = "刷新中...";
				setTimeout(() => {
					this.refreshing = false;
					this.refreshFlag = false;
					this.refreshText = "已刷新";
				}, 2000)
			},
			onpullingdown(e) {
				if (this.refreshing) {
					return;
				}

				this.pulling = false;
				if (Math.abs(e.pullingDistance) > Math.abs(e.viewHeight)) {
					this.refreshFlag = true;
					this.refreshText = "释放立即刷新";
				} else {
					this.refreshFlag = false;
					this.refreshText = "下拉可以刷新";
				}
			},
			change(e) {
				this.current = e.detail.current
			},
			selectStyle(index) {
				this.dotsStyles = this.dotStyle[index]
				this.styleIndex = index
			},
			selectMode(mode, index) {
				this.mode = mode
				this.modeIndex = index
				this.styleIndex = -1
				this.dotsStyles = this.dotStyle[0]
			},
			clickItem(e) {
				this.swiperDotIndex = e
			},
			onBanner(index) {
				console.log(22222, index);
			}
		}
	}
</script>

<style>
	/* #ifndef APP-PLUS */
	page {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* #endif */

	.page {
		flex: 1;
	}

	.flexible-view {
		background-color: #f823ff;
	}

	.tabs {
		flex: 1;
		flex-direction: column;
		overflow: hidden;
		background-color: #ffffff;
		/* #ifdef MP-ALIPAY || MP-BAIDU */
		height: 100vh;
		/* #endif */
	}

	.tab-bar {
		width: 750upx;
		height: 84upx;
		flex-direction: row;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}

	/* #ifndef APP-NVUE */
	.tab-bar ::-webkit-scrollbar {
		display: none;
		width: 0 !important;
		height: 0 !important;
		-webkit-appearance: none;
		background: transparent;
	}

	/* #endif */

	.scroll-view-indicator {
		position: relative;
		height: 2px;
		background-color: transparent;
	}

	.scroll-view-underline {
		position: absolute;
		top: 0;
		bottom: 0;
		width: 0;
		background-color: #007AFF;
	}

	.scroll-view-animation {
		transition-duration: 0.2s;
		transition-property: left;
	}

	.tab-bar-line {
		height: 1upx;
		background-color: #cccccc;
	}

	.tab-view {
		flex: 1;
	}

	.uni-tab-item {
		/* #ifndef APP-PLUS */
		display: inline-block;
		/* #endif */
		flex-wrap: nowrap;
		padding-left: 25px;
		padding-right: 25px;
	}

	.uni-tab-item-title {
		color: #555;
		font-size: 30upx;
		height: 80upx;
		line-height: 80upx;
		flex-wrap: nowrap;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}

	.uni-tab-item-title-active {
		color: #007AFF;
	}

	.swiper-item {
		flex: 1;
		flex-direction: column;
	}

	.swiper-page {
		flex: 1;
		flex-direction: column;
		position: absolute;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
	}
	.swiper-box {
			height: 200px;
		}
	
		.swiper-item {
			/* #ifndef APP-NVUE */
			display: flex;
			/* #endif */
			flex-direction: column;
			justify-content: center;
			align-items: center;
			height: 200px;
			color: #fff;
		}
	
		.swiper-item0 {
			background-color: #cee1fd;
		}
	
		.swiper-item1 {
			background-color: #b2cef7;
		}
	
		.swiper-item2 {
			background-color: #cee1fd;
		}
	
		.image {
			width: 750rpx;
		}
	
		/* #ifndef APP-NVUE */
		::v-deep .image img {
			-webkit-user-drag: none;
			-khtml-user-drag: none;
			-moz-user-drag: none;
			-o-user-drag: none;
			user-drag: none;
		}
	
		/* #endif */
	
		@media screen and (min-width: 500px) {
			.uni-swiper-dot-box {
				width: 400px;
				margin: 0 auto;
				margin-top: 8px;
			}
	
			.image {
				width: 100%;
			}
		}
	
		.uni-bg-red {
			background-color: #ff5a5f;
		}
	
		.uni-bg-green {
			background-color: #09bb07;
		}
	
		.uni-bg-blue {
			background-color: #007aff;
		}
	
		.example-body {
			/* #ifndef APP-NVUE */
			display: flex;
			/* #endif */
			flex-direction: row;
			padding: 20rpx;
		}
	
		.example-body-item {
	
			flex-direction: row;
			justify-content: center;
			align-items: center;
			margin: 15rpx;
			padding: 15rpx;
			height: 60rpx;
			/* #ifndef APP-NVUE */
			display: flex;
			padding: 0 15rpx;
			/* #endif */
			flex: 1;
			border-color: #e5e5e5;
			border-style: solid;
			border-width: 1px;
			border-radius: 5px;
		}
	
		.example-body-item-text {
			font-size: 28rpx;
			color: #333;
		}
	
		.example-body-dots {
			width: 16rpx;
			height: 16rpx;
			border-radius: 50px;
			background-color: #333333;
			margin-left: 10rpx;
		}
	
		.active {
			border-style: solid;
			border-color: #007aff;
			border-width: 1px;
		}
</style>
