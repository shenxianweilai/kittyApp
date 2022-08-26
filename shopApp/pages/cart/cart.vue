<template>
	<view>
		<uni-card cover="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png">
			<text>这是一个带封面和操作栏的卡片示例，此示例展示了封面插槽和操作栏插槽的用法。</text>
			<template v-slot:actions>
				<view class="card-actions">
					<view class="card-actions-item" @click="actionsClick('分享')">
						<uni-icons type="pengyouquan" size="18" color="#999"></uni-icons>
						<text class="card-actions-item-text">分享</text>
					</view>
					<view class="card-actions-item" @click="actionsClick('点赞')">
						<uni-icons type="heart" size="18" color="#999"></uni-icons>
						<text class="card-actions-item-text">点赞</text>
					</view>
					<view class="card-actions-item" @click="actionsClick('评论')">
						<uni-icons type="chatbubble" size="18" color="#999"></uni-icons>
						<text class="card-actions-item-text">评论</text>
					</view>
				</view>
			</template>
		</uni-card>
		<uni-collapse>
			<uni-collapse-item title="默认开启" :open="true">
				<text>折叠内容</text>
			</uni-collapse-item>
			<uni-collapse-item title="折叠内容">
					<text>折叠内容</text>
			</uni-collapse-item>
			<uni-collapse-item title="禁用状态" disabled>
				<text>折叠内容</text>
			</uni-collapse-item>
			<uni-collapse-item title="标题文字"
				thumb="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png">
				<view class="content">
					<text class="text">折叠内容主体，可自定义内容及样式</text>
				</view>
			</uni-collapse-item>
		</uni-collapse>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			actionsClick(val) {
				console.log(val)
			}
		}
	}
</script>

<style>

</style>
