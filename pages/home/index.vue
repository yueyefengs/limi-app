<template>
	<view class="home-container">
		<!-- Main Header -->
		<view class="main-header">
			<view class="header-greeting">你好，家长!</view>
			<view class="header-status-line">小智今天状态良好，电量充足</view>
			<view class="header-connection-status">
				<view class="status-dot"></view>
				<text>已连接 · 电量 {{ batteryLevel }}%</text>
			</view>
			<view class="usage-info-box">
				<view class="usage-item">
					<text class="value">{{ usageTime }}</text>
					<text class="label">今日使用时长</text>
				</view>
				<view class="usage-item">
					<text class="value">{{ timeLimit }}</text>
					<text class="label">时长限制</text>
				</view>
			</view>
		</view>

		<!-- Action Buttons -->
		<view class="action-buttons">
			<view class="action-button" @click="navigateTo('/pages/chat/history')">
				<text class="iconfont icon-history action-icon"></text>
				<text class="action-text">聊天记录</text>
			</view>
			<view class="action-button" @click="navigateTo('/pages/settings/parental-controls')">
				<text class="iconfont icon-shield action-icon"></text>
				<text class="action-text">家长控制</text>
			</view>
		</view>

		<!-- Recent Activity Section -->
		<view class="section recent-activity">
			<view class="section-header">
				<text class="section-title">近期活动</text>
				<text class="see-all" @click="navigateTo('/pages/profile/activity-log')">查看全部</text>
			</view>
			<view class="activity-card" v-for="(activity, index) in recentActivities" :key="index">
				<view class="activity-icon-wrapper">
					<text class="iconfont" :class="activity.icon"></text>
				</view>
				<view class="activity-info">
					<text class="activity-title">{{ activity.title }}</text>
					<text class="activity-details">{{ activity.details }}</text>
					<text class="activity-time">{{ activity.time }}</text>
				</view>
			</view>
		</view>

		<!-- Recommended Content Section -->
		<view class="section recommended-content">
			<view class="section-header">
				<text class="section-title">推荐内容</text>
				<text class="see-all" @click="navigateTo('/pages/content/index')">更多推荐</text>
			</view>
			<view class="content-card" v-for="(content, index) in recommendedContent" :key="index" @click="navigateTo('/pages/content/detail?id=' + content.id)">
				<image class="content-image" :src="content.image" mode="aspectFill"></image>
				<view class="content-info">
					<text class="content-title">{{ content.title }}</text>
					<text class="content-subtitle">{{ content.subtitle }}</text>
				</view>
			</view>
		</view>

		<!-- Bottom Navigation Bar -->
		<view class="navbar">
			<view class="nav-item active">
				<text class="iconfont icon-filled-home nav-icon"></text>
				<text>首页</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/chat/index')">
				<text class="iconfont icon-filled-chat nav-icon"></text>
				<text>聊天</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/content/index')">
				<text class="iconfont icon-filled-discover nav-icon"></text>
				<text>发现</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/profile/index')">
				<text class="iconfont icon-filled-profile nav-icon"></text>
				<text>我的</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			usageTime: '1:42',
			timeLimit: '2:30',
			batteryLevel: 78,
			recentActivities: [
				{
					title: '童话故事时间',
					details: '听了《小红帽》的故事',
					time: '15分钟前',
					icon: 'icon-book-read'
				},
				{
					title: '动物知识问答',
					details: '了解了长颈鹿的生活习性',
					time: '昨天',
					icon: 'icon-question-circle'
				}
			],
			recommendedContent: [
				{
					id: 1,
					title: '恐龙世界探险',
					subtitle: '适合4-7岁 · 科普知识',
					image: '/static/images/dinosaur.png'
				},
				{
					id: 2,
					title: '经典儿歌合集',
					subtitle: '适合2-5岁 · 音乐启蒙',
					image: '/static/images/songs.png'
				}
			]
		}
	},
	onLoad() {
		uni.hideTabBar(); // Hide default system tab bar
	},
	methods: {
		navigateTo(url) {
			uni.navigateTo({
				url: url,
				fail: (err) => {
					console.error("Navigation failed:", err);
					uni.showToast({ title: '页面开发中', icon: 'none' });
				}
			});
		},
		switchTab(url) {
			uni.switchTab({
				url: url,
				fail: (err) => {
					console.error("Tab switch failed:", err);
					uni.showToast({ title: '页面切换失败', icon: 'none' });
				}
			});
		}
	}
}
</script>

<style>
.home-container {
	background-color: #f4f5f9;
	padding-bottom: 140rpx; /* Add padding for the fixed navbar */
	min-height: 100vh;
}

/* Updated Main Header Styles */
.main-header {
	display: flex;
	flex-direction: column;
	background: linear-gradient(135deg, #7b7ff6, #a37af4);
	border-radius: 0 0 40rpx 40rpx;
	padding: 40rpx 30rpx;
	color: white;
}

.header-greeting {
	font-size: 44rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
}

.header-status-line {
	font-size: 28rpx;
	opacity: 0.9;
	margin-bottom: 20rpx;
}

.header-connection-status {
	display: inline-flex; /* Use inline-flex to keep elements on one line */
	align-items: center;
	background-color: rgba(255, 255, 255, 0.2);
	border-radius: 30rpx;
	padding: 8rpx 20rpx;
	font-size: 26rpx;
	margin-bottom: 30rpx;
	max-width: max-content; /* Ensure bubble doesn't stretch full width */
}

.status-dot {
	width: 16rpx;
	height: 16rpx;
	background-color: #ffffff;
	border-radius: 50%;
	margin-right: 15rpx;
}

.usage-info-box {
	display: flex;
	justify-content: space-between;
	background-color: rgba(255, 255, 255, 0.1);
	border-radius: 24rpx;
	padding: 30rpx 40rpx; /* Adjusted padding */
}

.usage-item {
	text-align: center;
}

.usage-item .value {
	font-size: 48rpx;
	font-weight: bold;
	margin-bottom: 8rpx;
}

.usage-item .label {
	font-size: 26rpx;
	opacity: 0.8;
}

/* Action Buttons Styles (no change needed) */
.action-buttons {
	display: flex;
	justify-content: space-between;
	margin: 40rpx 30rpx;
	gap: 30rpx;
}

.action-button {
	flex: 1;
	background-color: white;
	border-radius: 24rpx;
	padding: 30rpx 0;
	display: flex;
	align-items: center;
	justify-content: center;
	box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
	font-size: 30rpx;
	color: #333;
}

.action-icon {
	font-size: 44rpx;
	margin-right: 15rpx;
	color: #6c757d;
}

.action-button:first-child .action-icon {
	color: #4a90e2; /* Blue for history */
}
.action-button:last-child .action-icon {
	color: #f5a623; /* Orange for parental control (shield) */
}

/* Section Styles (no change needed) */
.section {
	margin: 40rpx 30rpx;
}

.section-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 30rpx;
}

.section-title {
	font-size: 36rpx;
	font-weight: bold;
	color: #333;
}

.see-all {
	font-size: 28rpx;
	color: #4a90e2;
}

/* Activity Card Styles (no change needed) */
.activity-card {
	background-color: white;
	border-radius: 24rpx;
	padding: 30rpx;
	display: flex;
	align-items: center;
	margin-bottom: 20rpx;
	box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.03);
}

.activity-icon-wrapper {
	width: 80rpx;
	height: 80rpx;
	border-radius: 40rpx;
	background-color: #eef1f8;
	display: flex;
	justify-content: center;
	align-items: center;
	margin-right: 25rpx;
}

.activity-icon-wrapper .iconfont {
	font-size: 44rpx;
	color: #4a90e2;
}

.activity-info {
	flex: 1;
}

.activity-title {
	font-size: 32rpx;
	color: #333;
	margin-bottom: 8rpx;
	display: block;
}

.activity-details {
	font-size: 28rpx;
	color: #666;
	margin-bottom: 8rpx;
	display: block;
}

.activity-time {
	font-size: 24rpx;
	color: #999;
}

/* Content Card Styles (no change needed) */
.content-card {
	background-color: white;
	border-radius: 24rpx;
	padding: 30rpx;
	display: flex;
	align-items: center;
	margin-bottom: 20rpx;
	box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.03);
}

.content-image {
	width: 140rpx;
	height: 140rpx;
	border-radius: 16rpx;
	margin-right: 25rpx;
	background-color: #eee; /* Placeholder color */
}

.content-info {
	flex: 1;
}

.content-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
	margin-bottom: 10rpx;
	display: block;
}

.content-subtitle {
	font-size: 26rpx;
	color: #666;
}

/* Bottom Navbar Styles */
.navbar {
	height: 120rpx;
	position: fixed;
	bottom: 0;
	left: 0;
	right: 0;
	background-color: white;
	display: flex;
	justify-content: space-around;
	align-items: center;
	border-top: 1px solid #eee;
	z-index: 100;
}

.nav-item {
	display: flex;
	flex-direction: column;
	align-items: center;
	color: #999;
	font-size: 24rpx;
	padding: 10rpx 0;
}

.nav-icon {
	font-size: 48rpx; /* Slightly larger icons */
	margin-bottom: 4rpx;
}

.nav-item.active {
	color: #4a90e2;
}

/* Iconfont definition */
@font-face {
	font-family: 'iconfont';  
	src: url('https://at.alicdn.com/t/font_8d5l8fzk5b87iudi.ttf') format('truetype');
}

.iconfont {
	font-family: "iconfont" !important;
	font-style: normal;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

/* Icons for nav bar (Update codes as needed for the specific filled look) */
.icon-filled-home:before { content: "\e6b8"; } /* Example: Using a filled home code */
.icon-filled-chat:before { content: "\e65f"; } /* Example: Using a filled chat code */
.icon-filled-discover:before { content: "\e68e"; } /* Example: Using a filled discover code */
.icon-filled-profile:before { content: "\e7ae"; } /* Example: Using a filled profile code */

/* Icons for activity list */
.icon-book-read:before { content: "\e7b8"; }
.icon-question-circle:before { content: "\e6a3"; }

/* Icons for action buttons */
.icon-history:before { content: "\e752"; }
.icon-shield:before { content: "\e608"; } /* Example: Using a shield icon for parental control */

</style> 