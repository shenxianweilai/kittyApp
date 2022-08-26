<template>
	<view class="container">
		<uni-card is-full :is-shadow="false">
		  <text class="uni-h6">面包屑导航显示当前页面的路径，快速返回之前的任意可跳转页面</text>
		</uni-card>
		<uni-badge size="small" :text="100" absolute="rightBottom" type="primary">
			<button type="default">右下</button>
		</uni-badge>
		<view class="example-body">
			<uni-badge class="uni-badge-left-margin" text="1" />
			<uni-badge class="uni-badge-left-margin" text="2" type="primary" />
			<uni-badge class="uni-badge-left-margin" text="34" type="success" />
			<uni-badge class="uni-badge-left-margin" text="45" type="warning" />
			<uni-badge class="uni-badge-left-margin" text="123" type="info" />
		</view>
		<uni-section title="基础用法" type="line" padding>
		  <uni-breadcrumb separator="/">
			<uni-breadcrumb-item v-for="(route,index) in routes" :key="index" :to="route.to">
			  {{route.name}}
			</uni-breadcrumb-item>
		  </uni-breadcrumb>
		</uni-section>
		<view>
			<!-- 插入模式 -->
			<uni-calendar class="uni-calendar--hook" :selected="info.selected" :showMonth="false" @change="change" @monthSwitch="monthSwitch" />
		</view>
		
		<uni-section class="hideOnPc" title="弹出模式" type="line"></uni-section>
		<view class="example-body hideOnPc">
			<button class="calendar-button" type="button" @click="open">打开日历</button>
		</view>
		<uni-calendar ref="calendar" class="uni-calendar--hook" :clear-date="true" :date="info.date" :insert="info.insert" :lunar="info.lunar" :startDate="info.startDate"
		 :endDate="info.endDate" :range="info.range" @confirm="confirm" @close="close"/>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				routes: [
				  {
					to: "/pages/home/home",
					name: "首页",
				  },
				  {
					to: "",
					name: "菜单 A",
				  },
				  {
					to: "",
					name: "菜单 B",
				  },
				],
				showCalendar: false,
				info: {
					lunar: true,
					range: true,
					insert: false,
					selected: []
				}
			}
		},
		methods: {
			open() {
				this.$refs.calendar.open()
			},
			close(){
				console.log('弹窗关闭');
			},
			change(e) {
				console.log('change 返回:', e)
				// 模拟动态打卡
				if (this.info.selected.length > 5) return
				this.info.selected.push({
					date: e.fulldate,
					info: '打卡'
				})
			},
			confirm(e) {
				console.log('confirm 返回:', e)
			},
			monthSwitch(e) {
				console.log('monthSwitchs 返回:', e)
			}

		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
	}
</style>
