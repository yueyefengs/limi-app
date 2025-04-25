<template>
	<view class="profile-container">
		<!-- iOS Status Bar -->
		<view class="status-bar">
			<view class="time">{{currentTime}}</view>
			<view class="icons">
				<text class="icon">📶</text>
				<text class="icon">📡</text>
				<text class="icon">🔋</text>
			</view>
		</view>
		
		<!-- Main Content Area -->
		<view class="content">
			<!-- Profile Header with Device Info -->
			<view class="profile-header">
				<view class="device-container">
					<view class="device-avatar">
						<text class="icon">🤖</text>
					</view>
					<view class="device-info">
						<text class="device-name">小智机器人</text>
						<view class="device-status">
							<view class="status-indicator"></view>
							<text class="device-status-text">已连接</text>
						</view>
					</view>
					<view class="device-battery">
						<text class="icon">🔋</text>
						<text>78%</text>
					</view>
				</view>
				
				<view class="device-details">
					<view class="detail-item">
						<text class="detail-value">{{userInfo.dailyLimit || "2:30"}}</text>
						<text class="detail-label">每日限制</text>
					</view>
					<view class="detail-item">
						<text class="detail-value">{{userInfo.age}}岁</text>
						<text class="detail-label">孩子年龄</text>
					</view>
					<view class="detail-item">
						<text class="detail-value">v2.3.1</text>
						<text class="detail-label">固件版本</text>
					</view>
				</view>
			</view>
			
			<!-- Character Settings -->
			<view class="menu-section">
				<view class="menu-header">角色设定</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/child-profile')">
					<view class="menu-icon">👤</view>
					<view class="menu-text">自定义人设</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/child-profiles')">
					<view class="menu-icon">🏷️</view>
					<view class="menu-text">兴趣标签</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/parental-controls')">
					<view class="menu-icon">🤖</view>
					<view class="menu-text">AI设置</view>
					<view class="menu-arrow">></view>
				</view>
			</view>
			
			<!-- Settings Menu -->
			<view class="menu-section">
				<view class="menu-header">设置</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/index')">
					<view class="menu-icon">📡</view>
					<view class="menu-text">连接设置</view>
					<view class="menu-arrow">></view>
				</view>
			</view>
			
			<!-- Support & About Menu -->
			<view class="menu-section">
				<view class="menu-header">支持与关于</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/content-filter')">
					<view class="menu-icon">🌐</view>
					<view class="menu-text">语言和地区</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/parental-controls')">
					<view class="menu-icon">🛡️</view>
					<view class="menu-text">隐私与安全</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/usage-control')">
					<view class="menu-icon">❓</view>
					<view class="menu-text">帮助中心</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="handleNavigation('/pages/settings/content-filter')">
					<view class="menu-icon">🔒</view>
					<view class="menu-text">隐私政策</view>
					<view class="menu-arrow">></view>
				</view>
				<view class="menu-item" @click="showAboutInfo">
					<view class="menu-icon">ℹ️</view>
					<view class="menu-text">关于小智</view>
					<view class="menu-arrow">></view>
				</view>
			</view>
			
			<view class="version-info">
				小智App v1.0.3 (2025042201)<br>
				©2025 小智智能科技有限公司
			</view>
		</view>
		
		<!-- Bottom Navigation Bar -->
		<view class="navbar">
			<view class="nav-item" @click="switchTab('/pages/home/index')">
				<text class="iconfont icon-home nav-icon"></text>
				<text>首页</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/chat/index')">
				<text class="iconfont icon-chat nav-icon"></text>
				<text>聊天</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/content/index')">
				<text class="iconfont icon-discover nav-icon"></text>
				<text>内容</text>
			</view>
			<view class="nav-item active">
				<text class="iconfont icon-profile nav-icon"></text>
				<text>我的</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			currentTime: '9:41',
			userInfo: {
				name: '小明',
				age: 5,
				gender: '男',
				avatar: '/static/images/avatar.png',
				interests: ['恐龙', '宇宙', '绘画'],
				parentContact: '138****1234',
				dailyLimit: '2:30'
			}
		}
	},
	onLoad() {
		// hide the default system tabBar to prevent duplicate navigation bars
		uni.hideTabBar();
		
		// Load user info from storage if available
		const storedUserInfo = uni.getStorageSync('userInfo');
		if (storedUserInfo) {
			// Merge with default values for any missing properties
			this.userInfo = {
				...this.userInfo,
				...storedUserInfo
			};
		}
		
		// Update time display
		this.updateTime();
		setInterval(this.updateTime, 60000); // Update every minute
	},
	methods: {
		updateTime() {
			const now = new Date();
			const hours = now.getHours();
			const minutes = now.getMinutes();
			this.currentTime = `${hours}:${minutes < 10 ? '0' + minutes : minutes}`;
		},
		handleNavigation(url) {
			// Navigate to the page with error handling
			uni.navigateTo({
				url: url,
				fail: (err) => {
					console.error('Navigation failed:', err);
					
					// Extract the page name from the URL
					const pageName = url.split('/').pop();
					
					// Try alternative paths for specific pages
					if (url.includes('child-profile')) {
						// For child profile specifically
						uni.navigateTo({
							url: '/pages/settings/child-profile',
							fail: () => {
								uni.showToast({
									title: '页面开发中，即将上线',
									icon: 'none'
								});
							}
						});
					} else if (url.includes('child-profiles')) {
						// For child profiles list
						uni.navigateTo({
							url: '/pages/settings/child-profiles',
							fail: () => {
								uni.showToast({
									title: '页面开发中，即将上线',
									icon: 'none'
								});
							}
						});
					} else {
						// For other pages
						uni.showToast({
							title: '页面开发中，即将上线',
							icon: 'none'
						});
					}
				}
			});
		},
		switchTab(url) {
			uni.switchTab({
				url: url
			});
		},
		showAboutInfo() {
			uni.showModal({
				title: '关于小智',
				content: '小智AI伴学应用是一款专为3-12岁儿童设计的人工智能学习助手，由小智智能科技有限公司开发。\n\n版本: v1.0.3\n构建号: 2025042201',
				showCancel: false,
				confirmText: '确定'
			});
		},
		logout() {
			uni.showModal({
				title: '提示',
				content: '确定要退出登录吗？',
				success: (res) => {
					if (res.confirm) {
						// Clear login info
						uni.removeStorageSync('token');
						
						// Navigate to login page
						uni.reLaunch({
							url: '/pages/login/index'
						});
					}
				}
			});
		}
	}
}
</script>

<style>
.profile-container {
	display: flex;
	flex-direction: column;
	min-height: 100vh;
	background-color: #f5f5f5;
}

.status-bar {
	background-color: #ffffff;
	padding: 10rpx 30rpx;
	display: flex;
	justify-content: space-between;
	font-size: 24rpx;
}

.icons {
	display: flex;
	gap: 10rpx;
}

.content {
	flex: 1;
	padding: 0;
	background-color: #f5f5f5;
}

.profile-header {
	background-color: #ffffff;
	padding: 30rpx;
	border-radius: 0 0 30rpx 30rpx;
	box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
}

.device-container {
	display: flex;
	align-items: center;
	margin-bottom: 40rpx;
}

.device-avatar {
	width: 100rpx;
	height: 100rpx;
	background-color: #e3f2fd;
	border-radius: 50%;
	display: flex;
	justify-content: center;
	align-items: center;
	margin-right: 20rpx;
}

.device-avatar .icon {
	font-size: 60rpx;
}

.device-info {
	flex: 1;
}

.device-name {
	font-size: 38rpx;
	font-weight: bold;
	color: #333;
	margin-bottom: 10rpx;
}

.device-status {
	display: flex;
	align-items: center;
}

.status-indicator {
	width: 16rpx;
	height: 16rpx;
	background-color: #4CAF50;
	border-radius: 50%;
	margin-right: 10rpx;
}

.device-status-text {
	font-size: 26rpx;
	color: #4CAF50;
}

.device-battery {
	display: flex;
	align-items: center;
	font-size: 26rpx;
	color: #666;
}

.device-battery .icon {
	margin-right: 6rpx;
	font-size: 32rpx;
}

.device-details {
	display: flex;
	justify-content: space-between;
	border-top: 2rpx solid #f0f0f0;
	padding-top: 30rpx;
}

.detail-item {
	display: flex;
	flex-direction: column;
	align-items: center;
}

.detail-value {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
	margin-bottom: 10rpx;
}

.detail-label {
	font-size: 24rpx;
	color: #999;
}

.menu-section {
	margin: 30rpx 30rpx 0;
	background-color: #fff;
	border-radius: 20rpx;
	overflow: hidden;
	box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.03);
}

.menu-header {
	padding: 20rpx 30rpx;
	font-size: 28rpx;
	color: #999;
	background-color: #f9f9f9;
}

.menu-item {
	display: flex;
	align-items: center;
	padding: 30rpx;
	border-bottom: 1rpx solid #f0f0f0;
}

.menu-item:last-child {
	border-bottom: none;
}

.menu-icon {
	margin-right: 20rpx;
	font-size: 40rpx;
	width: 60rpx;
	display: flex;
	justify-content: center;
}

.menu-text {
	flex: 1;
	font-size: 32rpx;
	color: #333;
}

.menu-arrow {
	color: #ccc;
	font-size: 32rpx;
}

.version-info {
	text-align: center;
	padding: 40rpx 0 140rpx; /* Add bottom padding to account for navbar */
	font-size: 24rpx;
	color: #999;
	line-height: 1.5;
}

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
	font-size: 44rpx;
	margin-bottom: 6rpx;
}

.nav-item.active {
	color: #2196F3;
}

@font-face {
	font-family: 'iconfont';  
	src: url('https://at.alicdn.com/t/font_8d5l8fzk5b87iudi.ttf') format('truetype');
}

.iconfont {
	font-family: "iconfont" !important;
	font-size: 44rpx;
	font-style: normal;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

.icon-home:before {
	content: "\e6b8";
}

.icon-chat:before {
	content: "\e65f";
}

.icon-discover:before {
	content: "\e68e";
}

.icon-profile:before {
	content: "\e7ae";
}
</style> 