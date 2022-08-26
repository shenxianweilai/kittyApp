<template>
	<view>
		<uni-section title="一般用法" type="line" padding>
			<uni-countdown :day="1" :hour="1" :minute="12" :second="40" />
		</uni-section>
		<uni-data-picker :localdata="items" popup-title="请选择班级" @change="onchange" @nodeclick="onnodeclick"></uni-data-picker>
		<uni-dateformat date="2020/10/20 20:20:20"></uni-dateformat>
		<button @click="showDrawer" type="primary">右侧弹出 显示Drawer</button>
		<uni-drawer ref="showRight" mode="right" :mask-click="false">
			<scroll-view style="height: 100%;" scroll-y="true">
				<button @click="closeDrawer" type="primary">关闭Drawer</button>
				<view v-for="item in 60" :key="item">可滚动内容 {{ item }}</view>
			</scroll-view>
		</uni-drawer>
		<uni-easyinput v-model="value" placeholder="请输入内容"></uni-easyinput>
		
		<!-- 输入框头部图标 -->
		<uni-easyinput prefixIcon="search" v-model="value" placeholder="请输入内容" @iconClick="onClick"></uni-easyinput>
		<!-- 展示输入框尾部图标 -->
		<uni-easyinput suffixIcon="search"  v-model="value" placeholder="请输入内容" @iconClick="onClick"></uni-easyinput>
		<uni-fab
			:pattern="pattern"
			:content="content"
			:horizontal="horizontal"
			:vertical="vertical"
			:direction="direction"
			@trigger="trigger"
		></uni-fab>
		<uni-file-picker 
			v-model="value" file-mediatype="all">
			<button>选择文件</button>
		</uni-file-picker>
		<uni-forms ref="form" :modelValue="formData" :rules="rules">
			<uni-forms-item label="姓名" name="name">
				<uni-easyinput type="text" v-model="formData.name" placeholder="请输入姓名" />
			</uni-forms-item>
			<uni-forms-item label="邮箱" name="email">
				<input class="input" v-model="formData.email" type="text" placeholder="请输入用户名" @input="binddata('email',$event.detail.value)" />
			</uni-forms-item>
		</uni-forms>
		<button @click="submit">Submit</button>
		<view class="goods-carts">
			<uni-goods-nav :options="options" :fill="true" :button-group="buttonGroup" @click="onClick"
				@buttonClick="buttonClick" />
		</view>
		<uni-list>
			<uni-list :border="true">
				<!-- 显示圆形头像 -->
				<uni-list-chat :avatar-circle="true" title="uni-app" avatar="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png" note="您收到一条新的消息" time="2020-02-02 20:20" ></uni-list-chat>
				<!-- 右侧带角标 -->
				<uni-list-chat title="uni-app" avatar="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png" note="您收到一条新的消息" time="2020-02-02 20:20" badge-text="12"></uni-list-chat>
				<!-- 头像显示圆点 -->
				<uni-list-chat title="uni-app" avatar="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png" note="您收到一条新的消息" time="2020-02-02 20:20" badge-positon="left" badge-text="dot"></uni-list-chat>
				<!-- 头像显示角标 -->
				<uni-list-chat title="uni-app" avatar="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png" note="您收到一条新的消息" time="2020-02-02 20:20" badge-positon="left" badge-text="99"></uni-list-chat>
				<!-- 显示多头像 -->
				<uni-list-chat title="uni-app" :avatar-list="avatarList" note="您收到一条新的消息" time="2020-02-02 20:20" badge-positon="left" badge-text="dot"></uni-list-chat>
				<!-- 自定义右侧内容 -->
				<uni-list-chat title="uni-app" :avatar-list="avatarList" note="您收到一条新的消息" time="2020-02-02 20:20" badge-positon="left" badge-text="dot">
					<view class="chat-custom-right">
						<text class="chat-custom-text">刚刚</text>
						<!-- 需要使用 uni-icons 请自行引入 -->
						<uni-icons type="star-filled" color="#999" size="18"></uni-icons>
					</view>
				</uni-list-chat>
			</uni-list>
		</uni-list>
		<uni-load-more status="more"></uni-load-more>
		<uni-nav-bar shadow left-icon="left" title="开启阴影" @clickLeft="back" />
		<uni-section title="文字滚动" subTitle="使用 scrollable 属性使通告滚动,此时 single 属性将失效,始终单行显示" type="line">
			<uni-notice-bar show-icon scrollable
				text="uni-app 版正式发布，开发一次，同时发布iOS、Android、H5、微信小程序、支付宝小程序、百度小程序、头条小程序等7大平台。" />
		</uni-section>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				items: [{
				  text: "一年级",
				  value: "1-0",
				  children: [
					{
					  text: "1.1班",
					  value: "1-1"
					},
					{
					  text: "1.2班",
					  value: "1-2"
					}
				  ]
				},
				{
				  text: "二年级",
				  value: "2-0"
				},
				{
				  text: "三年级",
				  value: "3-0"
				}],
				// 表单数据
				formData: {
					name: 'LiMing',
					email: 'dcloud@email.com'
				},
				rules: {
					// 对name字段进行必填验证
					name: {
						rules: [{
								required: true,
								errorMessage: '请输入姓名',
							},
							{
								minLength: 3,
								maxLength: 5,
								errorMessage: '姓名长度在 {minLength} 到 {maxLength} 个字符',
							}
						]
					},
					// 对email字段进行必填验证
					email: {
						rules: [{
							format: 'email',
							errorMessage: '请输入正确的邮箱地址',
						}]
					}
				},
				avatarList: [{
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
				}, {
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
				}, {
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
				}]
			}
		},
		methods: {
			onchange(e) {
				const value = e.detail.value
			  },
			  onnodeclick(node) {
			  },
			  showDrawer() {
			  				this.$refs.showRight.open();
			},
			closeDrawer() {
				this.$refs.showRight.close();
			},
			submit() {
				this.$refs.form.validate().then(res=>{
					console.log('表单数据信息：', res);
				}).catch(err =>{
					console.log('表单错误信息：', err);
				})
			}
		}
	}
</script>

<style>
.goods-carts {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		position: fixed;
		left: 0;
		right: 0;
		/* #ifdef H5 */
		left: var(--window-left);
		right: var(--window-right);
		/* #endif */
		bottom: 0px;
	}
</style>
