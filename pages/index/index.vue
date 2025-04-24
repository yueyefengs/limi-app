<template>
	<view class="home-container">
		<!-- Header Section -->
		<view class="header">
			<view class="user-profile" @click="navigateToProfile">
				<image :src="currentChild.avatar" mode="aspectFill" class="avatar"></image>
			</view>
			<view class="greeting">
				<text class="greeting-text">你好，{{ currentChild.name }}</text>
				<text class="subgreeting">今天想学点什么呢？</text>
			</view>
			<view class="header-actions">
				<view class="search-button" @click="navigateToSearch">
					<image src="/static/icons/search.png" mode="aspectFit" class="action-icon"></image>
				</view>
				<view class="notification-button" @click="navigateToNotifications">
					<image src="/static/icons/notification.png" mode="aspectFit" class="action-icon"></image>
					<view class="notification-badge" v-if="unreadNotifications > 0">{{ unreadNotifications }}</view>
				</view>
			</view>
		</view>
		
		<!-- Scroll Content -->
		<scroll-view scroll-y class="content-scroll" @scrolltolower="loadMoreContent">
			<!-- Daily Task Banner -->
			<view class="daily-task-banner" @click="navigateToDailyTask">
				<view class="task-info">
					<text class="task-title">每日任务</text>
					<text class="task-description">完成今天的学习任务，获得奖励！</text>
					<view class="progress-container">
						<view class="progress-bar">
							<view class="progress-fill" :style="{ width: dailyTaskProgress + '%' }"></view>
						</view>
						<text class="progress-text">{{ dailyTaskProgress }}%</text>
					</view>
				</view>
				<image src="/static/images/daily-task.png" mode="aspectFit" class="task-image"></image>
			</view>
			
			<!-- Quick Actions -->
			<view class="quick-actions">
				<view class="action-item" @click="startChat">
					<view class="action-icon-wrapper ai-bg">
						<image src="/static/icons/ai-chat.png" mode="aspectFit" class="quick-action-icon"></image>
					</view>
					<text class="action-title">智能对话</text>
				</view>
				<view class="action-item" @click="navigateToRecommended">
					<view class="action-icon-wrapper recommend-bg">
						<image src="/static/icons/recommend.png" mode="aspectFit" class="quick-action-icon"></image>
					</view>
					<text class="action-title">推荐内容</text>
				</view>
				<view class="action-item" @click="navigateToFavorites">
					<view class="action-icon-wrapper favorite-bg">
						<image src="/static/icons/favorite.png" mode="aspectFit" class="quick-action-icon"></image>
					</view>
					<text class="action-title">我的收藏</text>
				</view>
				<view class="action-item" @click="navigateToHistory">
					<view class="action-icon-wrapper history-bg">
						<image src="/static/icons/history.png" mode="aspectFit" class="quick-action-icon"></image>
					</view>
					<text class="action-title">最近学习</text>
				</view>
			</view>
			
			<!-- Recent Learning -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">最近学习</text>
					<text class="see-all" @click="navigateToHistory">查看全部</text>
				</view>
				
				<scroll-view scroll-x class="horizontal-scroll">
					<view class="horizontal-content">
						<view class="content-card" v-for="(item, index) in recentLearning" :key="index" @click="navigateToContent(item.id)">
							<image :src="item.image" mode="aspectFill" class="content-image"></image>
							<view class="content-info">
								<text class="content-title">{{ item.title }}</text>
								<view class="content-meta">
									<text class="content-category">{{ item.category }}</text>
									<text class="content-progress">{{ item.progress }}%</text>
								</view>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
			
			<!-- Recommended Content -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">推荐内容</text>
					<text class="see-all" @click="navigateToRecommended">查看全部</text>
				</view>
				
				<scroll-view scroll-x class="horizontal-scroll">
					<view class="horizontal-content">
						<view class="content-card" v-for="(item, index) in recommendedContent" :key="index" @click="navigateToContent(item.id)">
							<image :src="item.image" mode="aspectFill" class="content-image"></image>
							<view class="content-tag" v-if="item.isNew">新内容</view>
							<view class="content-info">
								<text class="content-title">{{ item.title }}</text>
								<view class="content-meta">
									<text class="content-category">{{ item.category }}</text>
									<text class="content-duration">{{ item.duration }}</text>
								</view>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
			
			<!-- Learning Categories -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">学习分类</text>
					<text class="see-all" @click="navigateToCategories">全部分类</text>
				</view>
				
				<view class="categories-grid">
					<view class="category-item" v-for="(category, index) in learningCategories" :key="index" @click="navigateToCategory(category.id)">
						<view class="category-icon-wrapper" :style="{ backgroundColor: category.backgroundColor }">
							<image :src="category.icon" mode="aspectFit" class="category-icon"></image>
						</view>
						<text class="category-name">{{ category.name }}</text>
					</view>
				</view>
			</view>
			
			<!-- Achievements -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">成就与徽章</text>
					<text class="see-all" @click="navigateToAchievements">查看全部</text>
				</view>
				
				<view class="achievements-panel">
					<view class="achievement-stats">
						<view class="stat-item">
							<text class="stat-value">{{ learningDaysStreak }}</text>
							<text class="stat-label">连续学习天数</text>
						</view>
						<view class="stat-item">
							<text class="stat-value">{{ totalLearningHours }}</text>
							<text class="stat-label">总学习时长(小时)</text>
						</view>
						<view class="stat-item">
							<text class="stat-value">{{ completedContentCount }}</text>
							<text class="stat-label">已完成内容</text>
						</view>
					</view>
					
					<view class="recent-badges">
						<text class="recent-badges-title">最近获得的徽章</text>
						<view class="badges-row">
							<view class="badge-item" v-for="(badge, index) in recentBadges" :key="index" @click="showBadgeDetail(badge)">
								<image :src="badge.image" mode="aspectFit" class="badge-image"></image>
								<text class="badge-name">{{ badge.name }}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>
		
		<!-- Tabbar -->
		<view class="tabbar">
			<view class="tab-item active">
				<image src="/static/icons/home-active.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">首页</text>
			</view>
			<view class="tab-item" @click="navigateToChat">
				<image src="/static/icons/chat.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">对话</text>
			</view>
			<view class="tab-item" @click="navigateToContent">
				<image src="/static/icons/content.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">内容</text>
			</view>
			<view class="tab-item" @click="navigateToSettings">
				<image src="/static/icons/settings.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">设置</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// Current active child
				currentChild: {
					id: 1,
					name: '小明',
					age: 5,
					avatar: '/static/images/avatar-1.png'
				},
				
				// Notifications
				unreadNotifications: 3,
				
				// Daily task
				dailyTaskProgress: 75,
				
				// Recent learning content
				recentLearning: [
					{
						id: 101,
						title: '数学乐园 - 加法游戏',
						category: '数学',
						image: '/static/images/content-math.png',
						progress: 80
					},
					{
						id: 102,
						title: '阅读故事 - 小红帽',
						category: '语文',
						image: '/static/images/content-chinese.png',
						progress: 65
					},
					{
						id: 103,
						title: 'ABC英语启蒙',
						category: '英语',
						image: '/static/images/content-english.png',
						progress: 30
					},
					{
						id: 104,
						title: '探索科学 - 太阳系',
						category: '科学',
						image: '/static/images/content-science.png',
						progress: 45
					}
				],
				
				// Recommended content
				recommendedContent: [
					{
						id: 201,
						title: '数学思维训练 - 数字推理',
						category: '数学',
						image: '/static/images/recommend-math.png',
						duration: '25分钟',
						isNew: true
					},
					{
						id: 202,
						title: '儿童英语会话 - 打招呼',
						category: '英语',
						image: '/static/images/recommend-english.png',
						duration: '15分钟',
						isNew: false
					},
					{
						id: 203,
						title: '趣味科学实验 - 水的张力',
						category: '科学',
						image: '/static/images/recommend-science.png',
						duration: '30分钟',
						isNew: true
					},
					{
						id: 204,
						title: '音乐启蒙 - 认识乐器',
						category: '艺术',
						image: '/static/images/recommend-art.png',
						duration: '20分钟',
						isNew: false
					}
				],
				
				// Learning categories
				learningCategories: [
					{
						id: 1,
						name: '数学',
						icon: '/static/icons/math.png',
						backgroundColor: '#e3f2fd'
					},
					{
						id: 2,
						name: '语文',
						icon: '/static/icons/chinese.png',
						backgroundColor: '#e8f5e9'
					},
					{
						id: 3,
						name: '英语',
						icon: '/static/icons/english.png',
						backgroundColor: '#fff3e0'
					},
					{
						id: 4,
						name: '科学',
						icon: '/static/icons/science.png',
						backgroundColor: '#f3e5f5'
					},
					{
						id: 5,
						name: '艺术',
						icon: '/static/icons/art.png',
						backgroundColor: '#fce4ec'
					},
					{
						id: 6,
						name: '音乐',
						icon: '/static/icons/music.png',
						backgroundColor: '#e0f7fa'
					},
					{
						id: 7,
						name: '体育',
						icon: '/static/icons/sports.png',
						backgroundColor: '#fffde7'
					},
					{
						id: 8,
						name: '更多',
						icon: '/static/icons/more.png',
						backgroundColor: '#f5f5f5'
					}
				],
				
				// Learning stats and achievements
				learningDaysStreak: 7,
				totalLearningHours: 38,
				completedContentCount: 24,
				
				// Recent badges
				recentBadges: [
					{
						id: 1,
						name: '数学大师',
						image: '/static/images/badge-math.png',
						description: '完成10个数学课程'
					},
					{
						id: 2,
						name: '阅读达人',
						image: '/static/images/badge-reading.png',
						description: '阅读15个故事'
					},
					{
						id: 3,
						name: '连续学习7天',
						image: '/static/images/badge-streak.png',
						description: '连续7天使用应用学习'
					}
				]
			}
		},
		onLoad() {
			// Load child profile and other data
			this.loadChildProfile();
			this.loadRecentContent();
			this.loadRecommendations();
			this.checkNotifications();
		},
		methods: {
			// Load data methods
			loadChildProfile() {
				// In a real app, this would load from storage or API
				console.log('Loading child profile');
			},
			
			loadRecentContent() {
				// In a real app, this would load from storage or API
				console.log('Loading recent content');
			},
			
			loadRecommendations() {
				// In a real app, this would fetch personalized recommendations
				console.log('Loading recommendations');
			},
			
			checkNotifications() {
				// In a real app, this would check for new notifications
				console.log('Checking notifications');
			},
			
			loadMoreContent() {
				// In a real app, this would load more content when reaching the bottom
				console.log('Loading more content');
			},
			
			// Navigation methods
			navigateToProfile() {
				uni.navigateTo({
					url: '/pages/settings/child-profiles'
				});
			},
			
			navigateToSearch() {
				uni.navigateTo({
					url: '/pages/search/index'
				});
			},
			
			navigateToNotifications() {
				uni.navigateTo({
					url: '/pages/notifications/index'
				});
			},
			
			navigateToDailyTask() {
				uni.navigateTo({
					url: '/pages/tasks/daily'
				});
			},
			
			startChat() {
				uni.navigateTo({
					url: '/pages/chat/index'
				});
			},
			
			navigateToRecommended() {
				uni.switchTab({
					url: '/pages/content/index'
				});
			},
			
			navigateToFavorites() {
				uni.navigateTo({
					url: '/pages/content/favorites'
				});
			},
			
			navigateToHistory() {
				uni.navigateTo({
					url: '/pages/content/history'
				});
			},
			
			navigateToContent(id) {
				uni.navigateTo({
					url: `/pages/content/detail?id=${id}`
				});
			},
			
			navigateToCategories() {
				uni.navigateTo({
					url: '/pages/content/categories'
				});
			},
			
			navigateToCategory(id) {
				uni.navigateTo({
					url: `/pages/content/category?id=${id}`
				});
			},
			
			navigateToAchievements() {
				uni.navigateTo({
					url: '/pages/profile/achievements'
				});
			},
			
			navigateToChat() {
				uni.switchTab({
					url: '/pages/chat/index'
				});
			},
			
			navigateToSettings() {
				uni.switchTab({
					url: '/pages/settings/index'
				});
			},
			
			showBadgeDetail(badge) {
				uni.showModal({
					title: badge.name,
					content: badge.description,
					showCancel: false
				});
			}
		}
	}
</script>

<style>
	.home-container {
		min-height: 100vh;
		background-color: #f5f5f5;
		padding-bottom: 120rpx;
	}

	/* Header Styles */
	.header {
		display: flex;
		align-items: center;
		padding: 80rpx 40rpx 40rpx;
		background-color: #fff;
	}

	.avatar {
		width: 90rpx;
		height: 90rpx;
		border-radius: 45rpx;
		background-color: #f0f0f0;
	}

	.greeting {
		flex: 1;
		margin-left: 20rpx;
	}

	.greeting-text {
		font-size: 36rpx;
		font-weight: bold;
		color: #333;
		display: block;
	}

	.subgreeting {
		font-size: 26rpx;
		color: #666;
		margin-top: 6rpx;
		display: block;
	}

	.header-actions {
		display: flex;
	}

	.search-button, .notification-button {
		width: 80rpx;
		height: 80rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
	}

	.action-icon {
		width: 48rpx;
		height: 48rpx;
	}

	.notification-badge {
		position: absolute;
		top: 5rpx;
		right: 5rpx;
		min-width: 36rpx;
		height: 36rpx;
		border-radius: 18rpx;
		background-color: #ff3b30;
		color: white;
		font-size: 22rpx;
		text-align: center;
		line-height: 36rpx;
		padding: 0 6rpx;
	}

	/* Content Scroll */
	.content-scroll {
		height: calc(100vh - 250rpx);
	}

	/* Daily Task Banner */
	.daily-task-banner {
		margin: 30rpx;
		background: linear-gradient(to right, #4facfe, #00f2fe);
		border-radius: 20rpx;
		padding: 30rpx;
		display: flex;
		height: 200rpx;
		position: relative;
		overflow: hidden;
	}

	.task-info {
		flex: 1;
		z-index: 1;
	}

	.task-title {
		font-size: 34rpx;
		font-weight: bold;
		color: white;
		margin-bottom: 10rpx;
		display: block;
	}

	.task-description {
		font-size: 26rpx;
		color: rgba(255, 255, 255, 0.9);
		margin-bottom: 20rpx;
		display: block;
	}

	.progress-container {
		display: flex;
		align-items: center;
	}

	.progress-bar {
		flex: 1;
		height: 20rpx;
		background-color: rgba(255, 255, 255, 0.3);
		border-radius: 10rpx;
		overflow: hidden;
		margin-right: 15rpx;
	}

	.progress-fill {
		height: 100%;
		background-color: white;
		border-radius: 10rpx;
	}

	.progress-text {
		font-size: 24rpx;
		color: white;
	}

	.task-image {
		width: 180rpx;
		height: 180rpx;
		position: absolute;
		right: 20rpx;
		bottom: 20rpx;
	}

	/* Quick Actions */
	.quick-actions {
		display: flex;
		justify-content: space-between;
		padding: 0 30rpx;
		margin-bottom: 40rpx;
	}

	.action-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 20%;
	}

	.action-icon-wrapper {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-bottom: 15rpx;
	}

	.ai-bg {
		background-color: #e1f5fe;
	}

	.recommend-bg {
		background-color: #e8f5e9;
	}

	.favorite-bg {
		background-color: #fff8e1;
	}

	.history-bg {
		background-color: #f3e5f5;
	}

	.quick-action-icon {
		width: 60rpx;
		height: 60rpx;
	}

	.action-title {
		font-size: 24rpx;
		color: #333;
		text-align: center;
	}

	/* Section Styles */
	.section {
		background-color: #fff;
		margin-bottom: 20rpx;
		padding: 30rpx;
	}

	.section-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 20rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: bold;
		color: #333;
	}

	.see-all {
		font-size: 26rpx;
		color: #2196f3;
	}

	/* Horizontal Scroll */
	.horizontal-scroll {
		white-space: nowrap;
	}

	.horizontal-content {
		display: flex;
	}

	.content-card {
		width: 300rpx;
		margin-right: 20rpx;
		border-radius: 16rpx;
		overflow: hidden;
		background-color: #fff;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);
		position: relative;
		flex-shrink: 0;
	}

	.content-image {
		width: 300rpx;
		height: 180rpx;
	}

	.content-tag {
		position: absolute;
		top: 10rpx;
		right: 10rpx;
		background-color: #ff3b30;
		color: white;
		font-size: 22rpx;
		padding: 4rpx 10rpx;
		border-radius: 6rpx;
	}

	.content-info {
		padding: 15rpx;
	}

	.content-title {
		font-size: 28rpx;
		color: #333;
		margin-bottom: 10rpx;
		white-space: normal;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		height: 80rpx;
	}

	.content-meta {
		display: flex;
		justify-content: space-between;
		font-size: 24rpx;
	}

	.content-category {
		color: #666;
	}

	.content-progress, .content-duration {
		color: #2196f3;
	}

	/* Categories Grid */
	.categories-grid {
		display: flex;
		flex-wrap: wrap;
		margin: 0 -10rpx;
	}

	.category-item {
		width: 25%;
		padding: 10rpx;
		box-sizing: border-box;
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-bottom: 20rpx;
	}

	.category-icon-wrapper {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-bottom: 10rpx;
	}

	.category-icon {
		width: 60rpx;
		height: 60rpx;
	}

	.category-name {
		font-size: 26rpx;
		color: #333;
		text-align: center;
	}

	/* Achievements Panel */
	.achievements-panel {
		background-color: #f8f8f8;
		border-radius: 16rpx;
		padding: 20rpx;
	}

	.achievement-stats {
		display: flex;
		justify-content: space-around;
		margin-bottom: 30rpx;
	}

	.stat-item {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.stat-value {
		font-size: 36rpx;
		font-weight: bold;
		color: #2196f3;
		margin-bottom: 6rpx;
	}

	.stat-label {
		font-size: 24rpx;
		color: #666;
		text-align: center;
	}

	.recent-badges-title {
		font-size: 28rpx;
		color: #333;
		margin-bottom: 20rpx;
		display: block;
	}

	.badges-row {
		display: flex;
		justify-content: space-around;
	}

	.badge-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 33.33%;
	}

	.badge-image {
		width: 100rpx;
		height: 100rpx;
		margin-bottom: 10rpx;
	}

	.badge-name {
		font-size: 24rpx;
		color: #333;
		text-align: center;
	}

	/* Tabbar */
	.tabbar {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 120rpx;
		background-color: #fff;
		display: flex;
		box-shadow: 0 -2rpx 10rpx rgba(0, 0, 0, 0.05);
	}

	.tab-item {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.tab-icon {
		width: 48rpx;
		height: 48rpx;
		margin-bottom: 6rpx;
	}

	.tab-text {
		font-size: 24rpx;
		color: #999;
	}

	.tab-item.active .tab-text {
		color: #2196f3;
	}
</style>
