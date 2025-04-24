<template>
	<view class="content-container">
		<!-- Header Section -->
		<view class="header">
			<view class="title-section">
				<text class="title">内容推荐</text>
			</view>
			<view class="search-button" @tap="navigateToSearch">
				<text class="search-icon">🔍</text>
			</view>
		</view>

		<!-- Main Content Scroll -->
		<scroll-view scroll-y class="content-scroll" @scrolltolower="loadMoreContent">
			<!-- Category Pills -->
			<scroll-view scroll-x class="category-scroll">
				<view class="category-list">
					<view 
						v-for="(category, index) in categories" 
						:key="index"
						:class="['category-pill', selectedCategoryId === category.id ? 'active' : '']"
						@tap="selectCategory(category.id)">
						{{ category.name }}
					</view>
				</view>
			</scroll-view>

			<!-- Featured Content -->
			<view class="section featured-section">
				<view class="section-header">
					<text class="section-title">精选内容</text>
					<text class="section-more" @tap="showMoreFeatured">更多</text>
				</view>

				<swiper class="featured-swiper" :indicator-dots="true" :autoplay="true" :interval="5000" :duration="500">
					<swiper-item v-for="(item, index) in featuredContent" :key="index" @tap="navigateToContentDetail(item.id)">
						<view class="featured-item">
							<image :src="item.coverImg" mode="aspectFill" class="featured-image"></image>
							<view class="featured-overlay">
								<view class="featured-tag" v-if="item.isNew">新内容</view>
								<view class="featured-info">
									<text class="featured-title">{{ item.title }}</text>
									<text class="featured-desc">{{ item.shortDescription }}</text>
								</view>
							</view>
						</view>
					</swiper-item>
				</swiper>
			</view>

			<!-- For You Section -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">为你推荐</text>
					<text class="section-more" @tap="showMoreRecommended">更多</text>
				</view>

				<view class="content-grid">
					<view 
						class="content-card" 
						v-for="(item, index) in recommendedContent" 
						:key="index"
						@tap="navigateToContentDetail(item.id)">
						<view class="content-card-inner">
							<image :src="item.coverImg" mode="aspectFill" class="content-image"></image>
							<view class="content-tag" v-if="item.isNew">新内容</view>
							<view class="content-info">
								<text class="content-title">{{ item.title }}</text>
								<view class="content-meta">
									<text class="content-category">{{ item.category }}</text>
									<text class="content-duration">{{ item.duration }}</text>
								</view>
								<view class="content-stats">
									<view class="stat">
										<text class="stat-icon">👍</text>
										<text class="stat-value">{{ item.likes }}</text>
									</view>
									<view class="stat">
										<text class="stat-icon">👀</text>
										<text class="stat-value">{{ item.views }}</text>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>

			<!-- Age-Appropriate Section -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">适合{{ currentChild.age }}岁</text>
					<text class="section-more" @tap="showMoreAgeAppropriate">更多</text>
				</view>

				<scroll-view scroll-x class="horizontal-scroll">
					<view class="horizontal-content">
						<view 
							class="horizontal-card" 
							v-for="(item, index) in ageAppropriateContent" 
							:key="index"
							@tap="navigateToContentDetail(item.id)">
							<image :src="item.coverImg" mode="aspectFill" class="horizontal-image"></image>
							<view class="content-tag small" v-if="item.isNew">新内容</view>
							<view class="horizontal-info">
								<text class="horizontal-title">{{ item.title }}</text>
								<view class="horizontal-meta">
									<text class="horizontal-category">{{ item.category }}</text>
									<text class="horizontal-duration">{{ item.duration }}</text>
								</view>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>

			<!-- Popular Topics Section -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">热门主题</text>
				</view>

				<view class="topics-grid">
					<view 
						class="topic-item" 
						v-for="(topic, index) in popularTopics" 
						:key="index"
						@tap="navigateToTopic(topic.id)"
						:style="{ backgroundColor: topic.bgColor }">
						<image :src="topic.icon" mode="aspectFit" class="topic-icon"></image>
						<text class="topic-name">{{ topic.name }}</text>
					</view>
				</view>
			</view>

			<!-- Recently Updated Section -->
			<view class="section">
				<view class="section-header">
					<text class="section-title">最近更新</text>
					<text class="section-more" @tap="showMoreRecent">更多</text>
				</view>

				<view class="content-list">
					<view 
						class="list-item" 
						v-for="(item, index) in recentlyUpdatedContent" 
						:key="index"
						@tap="navigateToContentDetail(item.id)">
						<image :src="item.coverImg" mode="aspectFill" class="list-image"></image>
						<view class="list-info">
							<text class="list-title">{{ item.title }}</text>
							<view class="list-meta">
								<text class="list-category">{{ item.category }}</text>
								<text class="list-duration">{{ item.duration }}</text>
							</view>
							<text class="list-update-time">{{ item.updateTime }}更新</text>
						</view>
						<view class="list-action">
							<text class="action-icon">▶️</text>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>

		<!-- Bottom Tab Bar -->
		<view class="tabbar">
			<view class="tab-item" @tap="navigateToHome">
				<image src="/static/icons/home.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">首页</text>
			</view>
			<view class="tab-item" @tap="navigateToChat">
				<image src="/static/icons/chat.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">对话</text>
			</view>
			<view class="tab-item active">
				<image src="/static/icons/content-active.png" mode="aspectFit" class="tab-icon"></image>
				<text class="tab-text">内容</text>
			</view>
			<view class="tab-item" @tap="navigateToSettings">
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
			currentChild: {
				id: 1,
				name: '小明',
				age: 5,
				avatar: '/static/images/child-avatar.png'
			},
			selectedCategoryId: 0,
			categories: [
				{ id: 0, name: '全部' },
				{ id: 1, name: '数学' },
				{ id: 2, name: '语文' },
				{ id: 3, name: '英语' },
				{ id: 4, name: '科学' },
				{ id: 5, name: '艺术' },
				{ id: 6, name: '音乐' },
				{ id: 7, name: '体育' }
			],
			featuredContent: [
				{
					id: 101,
					title: '卡通动物数学课',
					shortDescription: '和可爱的动物朋友一起学习数学，让基础计算变得有趣',
					coverImg: '/static/images/featured-math.png',
					isNew: true
				},
				{
					id: 102,
					title: '通过游戏认识字母',
					shortDescription: '互动游戏让孩子轻松学会26个英文字母',
					coverImg: '/static/images/featured-english.png',
					isNew: false
				},
				{
					id: 103,
					title: '恐龙世界探索',
					shortDescription: '穿越时空，了解史前巨兽的生活',
					coverImg: '/static/images/featured-science.png',
					isNew: true
				}
			],
			recommendedContent: [
				{
					id: 201,
					title: '数学思维训练 - 数字推理',
					category: '数学',
					coverImg: '/static/images/recommend-math.png',
					duration: '25分钟',
					isNew: true,
					likes: 328,
					views: 1240
				},
				{
					id: 202,
					title: '儿童英语会话 - 打招呼',
					category: '英语',
					coverImg: '/static/images/recommend-english.png',
					duration: '15分钟',
					isNew: false,
					likes: 246,
					views: 890
				},
				{
					id: 203,
					title: '趣味科学实验 - 水的张力',
					category: '科学',
					coverImg: '/static/images/recommend-science.png',
					duration: '30分钟',
					isNew: true,
					likes: 189,
					views: 720
				},
				{
					id: 204,
					title: '音乐启蒙 - 认识乐器',
					category: '艺术',
					coverImg: '/static/images/recommend-art.png',
					duration: '20分钟',
					isNew: false,
					likes: 157,
					views: 683
				}
			],
			ageAppropriateContent: [
				{
					id: 301,
					title: '数数123 - 初级数学',
					category: '数学',
					coverImg: '/static/images/age-math.png',
					duration: '20分钟',
					isNew: false
				},
				{
					id: 302,
					title: '颜色与形状',
					category: '认知',
					coverImg: '/static/images/age-shapes.png',
					duration: '15分钟',
					isNew: true
				},
				{
					id: 303,
					title: '动物朋友们',
					category: '科学',
					coverImg: '/static/images/age-animals.png',
					duration: '25分钟',
					isNew: false
				},
				{
					id: 304,
					title: '儿歌大集合',
					category: '音乐',
					coverImg: '/static/images/age-music.png',
					duration: '30分钟',
					isNew: true
				},
				{
					id: 305,
					title: '简单英语单词',
					category: '英语',
					coverImg: '/static/images/age-english.png',
					duration: '15分钟',
					isNew: false
				}
			],
			popularTopics: [
				{
					id: 1,
					name: '数学启蒙',
					icon: '/static/icons/topic-math.png',
					bgColor: '#e3f2fd'
				},
				{
					id: 2,
					name: '自然探索',
					icon: '/static/icons/topic-nature.png',
					bgColor: '#e8f5e9'
				},
				{
					id: 3,
					name: '英语绘本',
					icon: '/static/icons/topic-english.png',
					bgColor: '#fff3e0'
				},
				{
					id: 4,
					name: '音乐艺术',
					icon: '/static/icons/topic-art.png',
					bgColor: '#f3e5f5'
				}
			],
			recentlyUpdatedContent: [
				{
					id: 401,
					title: '趣味拼音课程',
					category: '语文',
					coverImg: '/static/images/recent-chinese.png',
					duration: '25分钟',
					updateTime: '今天'
				},
				{
					id: 402,
					title: '英语启蒙歌曲',
					category: '英语',
					coverImg: '/static/images/recent-english.png',
					duration: '15分钟',
					updateTime: '昨天'
				},
				{
					id: 403,
					title: '动物百科',
					category: '科学',
					coverImg: '/static/images/recent-science.png',
					duration: '30分钟',
					updateTime: '3天前'
				}
			]
		};
	},
	onLoad() {
		this.loadChildProfile();
		this.loadRecommendedContent();
	},
	methods: {
		loadChildProfile() {
			// In a real app, this would load from storage or API
			console.log('Loading child profile for content recommendations');
		},
		
		loadRecommendedContent() {
			// In a real app, this would load personalized content from API
			console.log('Loading personalized content recommendations');
		},
		
		loadMoreContent() {
			// Called when user scrolls to bottom
			console.log('Loading more content items');
		},
		
		selectCategory(categoryId) {
			this.selectedCategoryId = categoryId;
			// In a real app, this would filter content based on category
			console.log(`Selected category ID: ${categoryId}`);
		},
		
		navigateToSearch() {
			uni.navigateTo({
				url: '/pages/search/index'
			});
		},
		
		navigateToContentDetail(id) {
			uni.navigateTo({
				url: `/pages/content/detail?id=${id}`
			});
		},
		
		navigateToTopic(topicId) {
			uni.navigateTo({
				url: `/pages/content/topic?id=${topicId}`
			});
		},
		
		showMoreFeatured() {
			uni.navigateTo({
				url: '/pages/content/featured'
			});
		},
		
		showMoreRecommended() {
			uni.navigateTo({
				url: '/pages/content/recommended'
			});
		},
		
		showMoreAgeAppropriate() {
			uni.navigateTo({
				url: '/pages/content/age-appropriate'
			});
		},
		
		showMoreRecent() {
			uni.navigateTo({
				url: '/pages/content/recent'
			});
		},
		
		navigateToHome() {
			uni.switchTab({
				url: '/pages/index/index'
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
		}
	}
};
</script>

<style>
.content-container {
	min-height: 100vh;
	background-color: #f5f5f5;
	display: flex;
	flex-direction: column;
	padding-bottom: 120rpx;
}

/* Header Styles */
.header {
	padding: 30rpx 30rpx;
	background-color: #ffffff;
	display: flex;
	align-items: center;
	justify-content: space-between;
	position: sticky;
	top: 0;
	z-index: 10;
}

.title-section {
	flex: 1;
}

.title {
	font-size: 40rpx;
	font-weight: bold;
	color: #333333;
}

.search-button {
	width: 80rpx;
	height: 80rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: #f0f0f0;
	border-radius: 40rpx;
}

.search-icon {
	font-size: 40rpx;
}

/* Content Scroll */
.content-scroll {
	flex: 1;
}

/* Category Pills */
.category-scroll {
	padding: 20rpx 0;
	background-color: #ffffff;
	white-space: nowrap;
	border-bottom: 1rpx solid #f0f0f0;
}

.category-list {
	display: flex;
	padding: 0 20rpx;
}

.category-pill {
	padding: 10rpx 30rpx;
	background-color: #f0f0f0;
	border-radius: 30rpx;
	margin-right: 20rpx;
	font-size: 28rpx;
	color: #666666;
	flex-shrink: 0;
}

.category-pill.active {
	background-color: #2196f3;
	color: #ffffff;
}

/* Section Styles */
.section {
	margin: 20rpx 0;
	padding: 20rpx 30rpx;
	background-color: #ffffff;
}

.section-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 20rpx;
}

.section-title {
	font-size: 34rpx;
	font-weight: bold;
	color: #333333;
}

.section-more {
	font-size: 26rpx;
	color: #2196f3;
}

/* Featured Content */
.featured-section {
	padding-bottom: 40rpx;
}

.featured-swiper {
	height: 360rpx;
	border-radius: 16rpx;
	overflow: hidden;
}

.featured-item {
	width: 100%;
	height: 100%;
	position: relative;
}

.featured-image {
	width: 100%;
	height: 100%;
}

.featured-overlay {
	position: absolute;
	bottom: 0;
	left: 0;
	right: 0;
	padding: 30rpx;
	background: linear-gradient(to top, rgba(0,0,0,0.7), transparent);
}

.featured-tag {
	position: absolute;
	top: 20rpx;
	right: 20rpx;
	background-color: #ff3b30;
	color: white;
	font-size: 24rpx;
	padding: 6rpx 16rpx;
	border-radius: 6rpx;
}

.featured-info {
	color: #ffffff;
}

.featured-title {
	font-size: 36rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
	display: block;
}

.featured-desc {
	font-size: 26rpx;
	opacity: 0.9;
}

/* Content Grid */
.content-grid {
	display: flex;
	flex-wrap: wrap;
	margin: 0 -10rpx;
}

.content-card {
	width: 50%;
	padding: 10rpx;
	box-sizing: border-box;
}

.content-card-inner {
	background-color: #ffffff;
	border-radius: 16rpx;
	overflow: hidden;
	box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);
	position: relative;
}

.content-image {
	width: 100%;
	height: 200rpx;
	background-color: #f0f0f0;
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

.content-tag.small {
	font-size: 20rpx;
	padding: 2rpx 8rpx;
}

.content-info {
	padding: 15rpx;
}

.content-title {
	font-size: 28rpx;
	color: #333333;
	margin-bottom: 10rpx;
	display: block;
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
	margin-bottom: 10rpx;
}

.content-category {
	color: #666666;
}

.content-duration {
	color: #2196f3;
}

.content-stats {
	display: flex;
	font-size: 22rpx;
	color: #999999;
}

.stat {
	display: flex;
	align-items: center;
	margin-right: 20rpx;
}

.stat-icon {
	margin-right: 4rpx;
	font-size: 26rpx;
}

/* Horizontal Scroll */
.horizontal-scroll {
	white-space: nowrap;
}

.horizontal-content {
	display: flex;
}

.horizontal-card {
	width: 260rpx;
	margin-right: 20rpx;
	border-radius: 16rpx;
	overflow: hidden;
	background-color: #ffffff;
	box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);
	position: relative;
	flex-shrink: 0;
}

.horizontal-image {
	width: 260rpx;
	height: 160rpx;
	background-color: #f0f0f0;
}

.horizontal-info {
	padding: 15rpx;
}

.horizontal-title {
	font-size: 26rpx;
	color: #333333;
	margin-bottom: 6rpx;
	white-space: normal;
	overflow: hidden;
	text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	height: 74rpx;
}

.horizontal-meta {
	display: flex;
	justify-content: space-between;
	font-size: 22rpx;
}

.horizontal-category {
	color: #666666;
}

.horizontal-duration {
	color: #2196f3;
}

/* Topics Grid */
.topics-grid {
	display: flex;
	flex-wrap: wrap;
	margin: 0 -10rpx;
}

.topic-item {
	width: 25%;
	padding: 10rpx;
	box-sizing: border-box;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	height: 160rpx;
	border-radius: 16rpx;
}

.topic-icon {
	width: 60rpx;
	height: 60rpx;
	margin-bottom: 10rpx;
}

.topic-name {
	font-size: 26rpx;
	color: #333333;
	text-align: center;
}

/* Content List */
.content-list {
}

.list-item {
	display: flex;
	margin-bottom: 20rpx;
	padding-bottom: 20rpx;
	border-bottom: 1rpx solid #f0f0f0;
}

.list-item:last-child {
	margin-bottom: 0;
	padding-bottom: 0;
	border-bottom: none;
}

.list-image {
	width: 160rpx;
	height: 120rpx;
	border-radius: 8rpx;
	margin-right: 20rpx;
	background-color: #f0f0f0;
	flex-shrink: 0;
}

.list-info {
	flex: 1;
	overflow: hidden;
}

.list-title {
	font-size: 28rpx;
	color: #333333;
	margin-bottom: 10rpx;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
}

.list-meta {
	display: flex;
	font-size: 24rpx;
	margin-bottom: 6rpx;
}

.list-category {
	color: #666666;
	margin-right: 20rpx;
}

.list-duration {
	color: #2196f3;
}

.list-update-time {
	font-size: 22rpx;
	color: #999999;
}

.list-action {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 80rpx;
	flex-shrink: 0;
}

.action-icon {
	font-size: 40rpx;
}

/* Tabbar */
.tabbar {
	position: fixed;
	bottom: 0;
	left: 0;
	right: 0;
	height: 120rpx;
	background-color: #ffffff;
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
	color: #999999;
}

.tab-item.active .tab-text {
	color: #2196f3;
}
</style> 