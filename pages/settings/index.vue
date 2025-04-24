<template>
	<view class="settings-container">
		<!-- Header -->
		<view class="header">
			<view class="back-button" @click="navigateBack">
				<image src="/static/icons/back.png" mode="aspectFit" class="back-icon"></image>
			</view>
			<text class="header-title">设置</text>
		</view>
		
		<!-- User Profile Section -->
		<view class="profile-section" @click="navigateToProfile">
			<view class="profile-info">
				<image src="/static/images/avatar.png" mode="aspectFit" class="profile-avatar"></image>
				<view class="profile-text">
					<text class="profile-name">小明</text>
					<text class="profile-age">5岁</text>
				</view>
			</view>
			<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
		</view>
		
		<!-- Parental Controls Section -->
		<view class="settings-section">
			<text class="section-title">家长控制</text>
			<view class="setting-item" @click="navigateTo('/pages/settings/usage-control')">
				<view class="setting-left">
					<image src="/static/icons/usage-control.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">使用控制</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="navigateTo('/pages/settings/content-filter')">
				<view class="setting-left">
					<image src="/static/icons/content-filter.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">内容过滤</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="navigateTo('/pages/settings/notifications')">
				<view class="setting-left">
					<image src="/static/icons/notification.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">通知设置</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
		</view>
		
		<!-- Device Management Section -->
		<view class="settings-section">
			<text class="section-title">设备管理</text>
			<view class="setting-item" @click="navigateTo('/pages/settings/devices')">
				<view class="setting-left">
					<image src="/static/icons/devices.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">已连接设备</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="showDeviceInfo">
				<view class="setting-left">
					<image src="/static/icons/info.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">设备信息</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="checkForUpdates">
				<view class="setting-left">
					<image src="/static/icons/update.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">检查更新</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
		</view>
		
		<!-- Support Section -->
		<view class="settings-section">
			<text class="section-title">帮助与支持</text>
			<view class="setting-item" @click="navigateTo('/pages/settings/help-center')">
				<view class="setting-left">
					<image src="/static/icons/help.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">帮助中心</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="navigateTo('/pages/settings/feedback')">
				<view class="setting-left">
					<image src="/static/icons/feedback.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">意见反馈</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
			<view class="setting-item" @click="navigateTo('/pages/settings/privacy')">
				<view class="setting-left">
					<image src="/static/icons/privacy.png" mode="aspectFit" class="setting-icon"></image>
					<text class="setting-text">隐私政策</text>
				</view>
				<image src="/static/icons/arrow-right.png" mode="aspectFit" class="arrow-icon"></image>
			</view>
		</view>
		
		<!-- Account Section -->
		<view class="settings-section">
			<view class="setting-item logout" @click="handleLogout">
				<text class="logout-text">退出登录</text>
			</view>
		</view>
		
		<!-- App Version -->
		<view class="app-version">
			<text>小智App v1.0.0</text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {}
	},
	methods: {
		navigateBack() {
			uni.navigateBack()
		},
		navigateTo(url) {
			uni.navigateTo({
				url: url
			})
		},
		navigateToProfile() {
			uni.navigateTo({
				url: '/pages/profile/index'
			})
		},
		showDeviceInfo() {
			const deviceInfo = uni.getSystemInfoSync()
			uni.showModal({
				title: '设备信息',
				content: `设备品牌: ${deviceInfo.brand}\n设备型号: ${deviceInfo.model}\n系统: ${deviceInfo.platform} ${deviceInfo.system}\n屏幕: ${deviceInfo.screenWidth}x${deviceInfo.screenHeight}`,
				showCancel: false
			})
		},
		checkForUpdates() {
			uni.showLoading({
				title: '检查更新中...'
			})
			
			setTimeout(() => {
				uni.hideLoading()
				uni.showModal({
					title: '更新检查',
					content: '您的应用已是最新版本',
					showCancel: false
				})
			}, 1500)
		},
		handleLogout() {
			uni.showModal({
				title: '退出登录',
				content: '确定要退出登录吗？',
				success: (res) => {
					if (res.confirm) {
						// 清除登录信息
						uni.removeStorageSync('token')
						uni.removeStorageSync('userInfo')
						
						// 跳转到登录页面
						uni.reLaunch({
							url: '/pages/login/index'
						})
					}
				}
			})
		}
	}
}
</script>

<style>
.settings-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	padding-bottom: 40rpx;
}

.header {
	position: relative;
	display: flex;
	align-items: center;
	justify-content: center;
	height: 90rpx;
	background-color: #fff;
	padding: 0 30rpx;
}

.back-button {
	position: absolute;
	left: 30rpx;
	top: 50%;
	transform: translateY(-50%);
}

.back-icon {
	width: 40rpx;
	height: 40rpx;
}

.header-title {
	font-size: 36rpx;
	font-weight: 500;
}

.profile-section {
	display: flex;
	align-items: center;
	justify-content: space-between;
	background-color: #fff;
	padding: 30rpx;
	margin-top: 20rpx;
}

.profile-info {
	display: flex;
	align-items: center;
}

.profile-avatar {
	width: 120rpx;
	height: 120rpx;
	border-radius: 60rpx;
	margin-right: 20rpx;
}

.profile-text {
	display: flex;
	flex-direction: column;
}

.profile-name {
	font-size: 32rpx;
	font-weight: 500;
	margin-bottom: 10rpx;
}

.profile-age {
	font-size: 28rpx;
	color: #666;
}

.settings-section {
	background-color: #fff;
	margin-top: 20rpx;
	padding: 0 30rpx;
}

.section-title {
	font-size: 28rpx;
	color: #666;
	padding: 20rpx 0;
}

.setting-item {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 26rpx 0;
	border-top: 1px solid #eee;
}

.setting-left {
	display: flex;
	align-items: center;
}

.setting-icon {
	width: 48rpx;
	height: 48rpx;
	margin-right: 20rpx;
}

.setting-text {
	font-size: 30rpx;
	color: #333;
}

.arrow-icon {
	width: 32rpx;
	height: 32rpx;
}

.logout {
	justify-content: center;
	border-top: none;
}

.logout-text {
	color: #ff3b30;
	font-size: 32rpx;
}

.app-version {
	text-align: center;
	margin-top: 60rpx;
	color: #999;
	font-size: 24rpx;
}
</style> 