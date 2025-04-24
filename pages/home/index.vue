<template>
	<view class="home-container">
		<!-- Status Bar -->
		<view class="status-bar">
			<text class="time">{{ currentTime }}</text>
			<view class="icons">
				<text class="iconfont icon-signal"></text>
				<text class="iconfont icon-wifi"></text>
				<text class="iconfont icon-battery-full"></text>
			</view>
		</view>
		
		<!-- Dashboard Header -->
		<view class="dashboard-header">
			<text class="header-title">你好，小朋友！</text>
			<text class="header-subtitle">今天是个美好的学习日~</text>
			<view class="device-status">
				<text class="iconfont icon-bluetooth"></text>
				<text>已连接智能玩具</text>
			</view>
			
			<view class="usage-stats">
				<view class="usage-item">
					<text class="value">45分钟</text>
					<text class="label">今日使用时长</text>
				</view>
				<view class="usage-item">
					<text class="value">92分</text>
					<text class="label">学习积分</text>
				</view>
			</view>
		</view>
		
		<!-- Stats Row -->
		<view class="stats-row">
			<view class="stat-card">
				<text class="number">5</text>
				<text class="label">故事</text>
			</view>
			<view class="stat-card">
				<text class="number">3</text>
				<text class="label">游戏</text>
			</view>
			<view class="stat-card">
				<text class="number">12</text>
				<text class="label">对话</text>
			</view>
		</view>
		
		<!-- Quick Actions -->
		<view class="quick-actions">
			<view class="action-button" @click="navigateTo('/pages/chat/index')">
				<view class="action-icon">
					<text class="iconfont icon-message"></text>
				</view>
				<text class="action-text">聊一聊</text>
			</view>
			<view class="action-button" @click="navigateTo('/pages/content/index')">
				<view class="action-icon">
					<text class="iconfont icon-play"></text>
				</view>
				<text class="action-text">听故事</text>
			</view>
			<view class="action-button" @click="navigateTo('/pages/profile/index')">
				<view class="action-icon">
					<text class="iconfont icon-user"></text>
				</view>
				<text class="action-text">我的</text>
			</view>
		</view>
		
		<!-- Recent Activity Section -->
		<view class="activity-section">
			<view class="section-header">
				<text class="section-title">最近活动</text>
				<text class="see-all" @click="navigateTo('/pages/profile/index')">查看全部</text>
			</view>
			
			<view class="activity-card" v-for="(activity, index) in recentActivities" :key="index">
				<view class="activity-icon">
					<text class="iconfont" :class="activity.icon"></text>
				</view>
				<view class="activity-info">
					<text class="activity-title">{{ activity.title }}</text>
					<text class="activity-subtitle">{{ activity.subtitle }}</text>
					<text class="activity-time">{{ activity.time }}</text>
				</view>
			</view>
		</view>
		
		<!-- Recommended Content Section -->
		<view class="content-section">
			<view class="section-header">
				<text class="section-title">推荐内容</text>
				<text class="see-all" @click="navigateTo('/pages/content/index')">查看全部</text>
			</view>
			
			<view class="content-card" v-for="(content, index) in recommendedContent" :key="index" @click="navigateTo('/pages/content/detail?id=' + content.id)">
				<image class="content-image" :src="content.image" mode="aspectFill"></image>
				<view class="content-info">
					<text class="content-title">{{ content.title }}</text>
					<text class="content-subtitle">{{ content.subtitle }}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			currentTime: '9:41',
			recentActivities: [
				{
					title: '完成了"恐龙世界"故事',
					subtitle: '听故事并回答了问题',
					time: '今天 14:30',
					icon: 'icon-book'
				},
				{
					title: '进行了15分钟的聊天',
					subtitle: '聊了关于动物的话题',
					time: '今天 10:15',
					icon: 'icon-message'
				},
				{
					title: '完成了数字配对游戏',
					subtitle: '获得了5颗星',
					time: '昨天 16:20',
					icon: 'icon-game-controller'
				}
			],
			recommendedContent: [
				{
					id: 1,
					title: '小兔子的奇幻冒险',
					subtitle: '适合3-5岁 | 10分钟',
					image: '/static/images/story1.jpg'
				},
				{
					id: 2,
					title: '认识动物朋友们',
					subtitle: '适合4-6岁 | 互动游戏',
					image: '/static/images/game1.jpg'
				},
				{
					id: 3,
					title: '数字王国大探险',
					subtitle: '适合5-7岁 | 学习数学',
					image: '/static/images/learn1.jpg'
				}
			]
		}
	},
	methods: {
		navigateTo(url) {
			uni.navigateTo({
				url: url
			})
		},
		updateTime() {
			const now = new Date()
			const hours = now.getHours()
			const minutes = now.getMinutes()
			this.currentTime = `${hours}:${minutes < 10 ? '0' + minutes : minutes}`
		}
	},
	onLoad() {
		this.updateTime()
		// 更新时间的定时器，每分钟更新一次
		setInterval(() => {
			this.updateTime()
		}, 60000)
	}
}
</script>

<style>
.home-container {
	padding: 0 30rpx 120rpx;
}

.dashboard-header {
	background: linear-gradient(135deg, #4a90e2, #7454e9);
	color: white;
	border-radius: 30rpx;
	padding: 40rpx;
	margin-bottom: 40rpx;
	position: relative;
}

.header-title {
	font-size: 44rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
	display: block;
}

.header-subtitle {
	font-size: 28rpx;
	opacity: 0.9;
	margin-bottom: 20rpx;
	display: block;
}

.device-status {
	display: inline-block;
	background-color: rgba(255, 255, 255, 0.2);
	border-radius: 30rpx;
	padding: 8rpx 20rpx;
	font-size: 24rpx;
}

.device-status .iconfont {
	margin-right: 10rpx;
}

.usage-stats {
	display: flex;
	justify-content: space-between;
	margin-top: 30rpx;
}

.usage-item {
	text-align: center;
	background-color: rgba(255, 255, 255, 0.1);
	border-radius: 24rpx;
	padding: 20rpx;
	flex-basis: 48%;
}

.usage-item .value {
	font-size: 44rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
	display: block;
}

.usage-item .label {
	font-size: 24rpx;
	opacity: 0.9;
}

.stats-row {
	display: flex;
	justify-content: space-between;
	margin-bottom: 40rpx;
}

.stat-card {
	background-color: white;
	border-radius: 24rpx;
	padding: 30rpx;
	width: 30%;
	text-align: center;
	box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
}

.stat-card .number {
	font-size: 40rpx;
	font-weight: bold;
	color: #4a90e2;
	margin-bottom: 10rpx;
	display: block;
}

.stat-card .label {
	font-size: 24rpx;
	color: #6c757d;
}

.quick-actions {
	display: flex;
	gap: 20rpx;
	margin-bottom: 40rpx;
}

.action-button {
	display: flex;
	align-items: center;
	background-color: white;
	border-radius: 20rpx;
	padding: 20rpx 30rpx;
	box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
	flex: 1;
}

.action-icon {
	width: 80rpx;
	height: 80rpx;
	border-radius: 20rpx;
	background-color: #f0f5ff;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-right: 20rpx;
	color: #4a90e2;
}

.action-text {
	font-size: 28rpx;
	font-weight: 500;
}

.activity-section, .content-section {
	margin-bottom: 40rpx;
}

.section-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 30rpx;
}

.section-title {
	font-size: 36rpx;
	font-weight: 600;
}

.see-all {
	color: #4a90e2;
	font-size: 28rpx;
}

.activity-card, .content-card {
	background-color: white;
	border-radius: 24rpx;
	overflow: hidden;
	margin-bottom: 20rpx;
	box-shadow: 0 4rpx 10rpx rgba(0,0,0,0.05);
	display: flex;
	align-items: center;
}

.activity-icon {
	width: 100rpx;
	height: 100rpx;
	background-color: #f8f9fa;
	border-radius: 24rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	margin: 20rpx;
	color: #4a90e2;
	font-size: 40rpx;
}

.activity-info, .content-info {
	flex: 1;
	padding: 20rpx;
}

.activity-title, .content-title {
	font-size: 32rpx;
	font-weight: 500;
	margin-bottom: 10rpx;
	display: block;
}

.activity-subtitle, .content-subtitle {
	font-size: 26rpx;
	color: #6c757d;
	display: block;
}

.activity-time {
	font-size: 24rpx;
	color: #6c757d;
	margin-top: 10rpx;
	display: block;
}

.content-image {
	width: 140rpx;
	height: 140rpx;
	object-fit: cover;
	margin: 20rpx;
	border-radius: 16rpx;
}
</style> 