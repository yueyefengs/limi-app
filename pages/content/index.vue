<template>
	<view class="content-page-container">
		<!-- Status Bar -->
		<view class="status-bar">
			<text class="time">{{ currentTime }}</text>
			<view class="icons">
				<text class="iconfont icon-signal"></text>
				<text class="iconfont icon-wifi"></text>
				<text class="iconfont icon-battery-full"></text>
			</view>
		</view>

		<!-- Header -->
		<view class="header">
			<text class="header-title">内容推荐</text>
			<view class="search-icon-wrapper" @tap="navigateToSearch">
				<text class="iconfont icon-search"></text>
			</view>
		</view>

		<!-- Scrollable Content Area -->
		<scroll-view scroll-y class="scroll-content">
			<!-- Grid Content Section 1 -->
			<view class="content-grid">
				<view class="grid-item" v-for="(item, index) in gridContent1" :key="'grid1-' + index" @tap="navigateToContentDetail(item.id)">
					<view class="item-header">
						<text class="item-title">{{ item.title }}</text>
						<text class="item-duration">{{ item.duration }}</text>
					</view>
					<text class="item-category">{{ item.category }}</text>
					<view class="item-stats">
						<text class="stat-icon">👍</text>
						<text class="stat-value">{{ item.likes }}</text>
						<text class="stat-icon eye">👀</text>
						<text class="stat-value">{{ item.views }}</text>
					</view>
				</view>
			</view>

			<!-- Grid Content Section 2 (with New Tag) -->
			<view class="content-grid">
				<view class="grid-item image-bg" v-for="(item, index) in gridContent2" :key="'grid2-' + index" @tap="navigateToContentDetail(item.id)">
					<image :src="item.image" class="bg-image" mode="aspectFill"></image>
					<view class="item-overlay"></view>
					<view class="new-tag" v-if="item.isNew">新内容</view>
					<view class="item-content">
						<text class="item-title-overlay">{{ item.title }}</text>
						<text class="item-category-overlay">{{ item.category }}</text>
						<view class="item-duration-overlay">{{ item.duration }}</view>
					</view>
					<view class="item-stats-overlay">
						<text class="stat-icon">👍</text>
						<text class="stat-value">{{ item.likes }}</text>
						<text class="stat-icon eye">👀</text>
						<text class="stat-value">{{ item.views }}</text>
					</view>
				</view>
			</view>

			<!-- Age Appropriate Section -->
			<view class="section age-section">
				<view class="section-header">
					<text class="section-title">适合5岁</text>
					<text class="section-more" @tap="showMoreAgeAppropriate(5)">更多</text>
				</view>
				<scroll-view scroll-x class="horizontal-scroll">
					<view class="horizontal-list">
						<view class="horizontal-item" v-for="(item, index) in ageAppropriateContent" :key="'age5-' + index" @tap="navigateToContentDetail(item.id)">
							<view class="item-image-placeholder"></view>
							<view class="new-tag small" v-if="item.isNew">新内容</view>
							<text class="item-title-horizontal">{{ item.title }}</text>
						</view>
					</view>
				</scroll-view>
			</view>
		</scroll-view>

		<!-- Bottom Navigation Bar -->
		<view class="navbar">
			<view class="nav-item" @click="switchTab('/pages/home/index')">
				<text class="iconfont icon-filled-home nav-icon"></text>
				<text>首页</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/chat/index')">
				<text class="iconfont icon-filled-chat nav-icon"></text>
				<text>对话</text>
			</view>
			<view class="nav-item active">
				<text class="iconfont icon-filled-discover nav-icon"></text>
				<text>内容</text>
			</view>
			<view class="nav-item" @click="switchTab('/pages/profile/index')">
				<text class="iconfont icon-filled-profile nav-icon"></text>
				<text>设置</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			currentTime: '13:56',
			gridContent1: [
				{
					id: 201, title: '数学思维训练 - 数字推理', category: '数学', duration: '25分钟', likes: 328, views: 1240
				},
				{
					id: 202, title: '儿童英语会话 - 打招呼', category: '英语', duration: '15分钟', likes: 246, views: 890
				}
			],
			gridContent2: [
				{
					id: 301, title: '趣味科学实验 - 水的张力', category: '科学', duration: '30分钟', likes: 189, views: 720, isNew: true, image: '/static/images/science-exp.png'
				},
				{
					id: 302, title: '音乐启蒙 - 认识乐器', category: '艺术', duration: '20分钟', likes: 157, views: 683, isNew: false, image: '/static/images/music-inst.png'
				}
			],
			ageAppropriateContent: [
				{
					id: 401, title: '数数123 - 初级数学', isNew: false
				},
				{
					id: 402, title: '颜色与形状', isNew: true
				},
				{
					id: 403, title: '动物朋友们', isNew: false
				},
				{
					id: 404, title: '简单线条画', isNew: false
				}
			]
		}
	},
	onLoad() {
		uni.hideTabBar(); // Hide default system tab bar
		this.updateTime();
		setInterval(this.updateTime, 60000);
	},
	methods: {
		updateTime() {
			const now = new Date();
			const hours = now.getHours();
			const minutes = now.getMinutes();
			this.currentTime = `${hours}:${minutes < 10 ? '0' + minutes : minutes}`;
		},
		navigateToSearch() {
			console.log("Navigate to search");
			// uni.navigateTo({ url: '/pages/search/index' }); // Implement search page navigation
			uni.showToast({ title: '搜索功能待实现', icon: 'none' });
		},
		navigateToContentDetail(id) {
			uni.navigateTo({ url: `/pages/content/detail?id=${id}` });
		},
		showMoreAgeAppropriate(age) {
			console.log(`Show more content for age ${age}`);
			// uni.navigateTo({ url: `/pages/content/list?age=${age}` }); // Implement list page navigation
			uni.showToast({ title: '更多内容待实现', icon: 'none' });
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
.content-page-container {
	display: flex;
	flex-direction: column;
	height: 100vh;
	background-color: #f4f5f9;
}

.status-bar {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 10rpx 30rpx;
	padding-top: var(--status-bar-height);
	background-color: #fff;
}

.status-bar .time {
	font-size: 30rpx;
	font-weight: 500;
}

.status-bar .icons {
	display: flex;
	gap: 15rpx;
}
.status-bar .icons .iconfont {
	font-size: 36rpx;
	color: #666;
}

.header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 20rpx 30rpx;
	background-color: #fff;
}

.header-title {
	font-size: 44rpx;
	font-weight: bold;
}

.search-icon-wrapper {
	width: 70rpx;
	height: 70rpx;
	border-radius: 50%;
	background-color: #f0f1f5;
	display: flex;
	justify-content: center;
	align-items: center;
}

.search-icon-wrapper .iconfont {
	font-size: 40rpx;
	color: #666;
}

.scroll-content {
	flex: 1;
	height: 0; /* Necessary for scroll-view height calculation */
	padding: 0 30rpx;
	padding-bottom: 140rpx; /* Space for navbar */
}

.content-grid {
	display: grid;
	grid-template-columns: repeat(2, 1fr);
	gap: 20rpx;
	margin-top: 30rpx;
}

.grid-item {
	background-color: #fff;
	border-radius: 20rpx;
	padding: 25rpx;
	box-shadow: 0 4rpx 15rpx rgba(0, 0, 0, 0.04);
	position: relative;
	overflow: hidden;
}

.item-header {
	display: flex;
	justify-content: space-between;
	align-items: baseline;
	margin-bottom: 10rpx;
}

.item-title {
	font-size: 32rpx;
	font-weight: bold;
	color: #333;
}

.item-duration {
	font-size: 24rpx;
	color: #999;
}

.item-category {
	font-size: 26rpx;
	color: #666;
	margin-bottom: 20rpx;
	display: block;
}

.item-stats {
	display: flex;
	align-items: center;
	font-size: 24rpx;
	color: #999;
}

.stat-icon {
	margin-right: 5rpx;
}

.stat-icon.eye {
	margin-left: 20rpx;
}

/* Styles for grid items with image background */
.grid-item.image-bg {
	padding: 0;
	height: 280rpx; /* Fixed height for image background items */
	display: flex;
	flex-direction: column;
	justify-content: space-between;
}

.bg-image {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	z-index: 1;
}

.item-overlay {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background: linear-gradient(to top, rgba(0,0,0,0.4), rgba(0,0,0,0));
	z-index: 2;
}

.new-tag {
	position: absolute;
	top: 15rpx;
	right: 15rpx;
	background-color: #ff4d4f;
	color: white;
	font-size: 20rpx;
	padding: 4rpx 10rpx;
	border-radius: 6rpx;
	z-index: 3;
}

.new-tag.small {
    top: 10rpx;
    right: 10rpx;
    font-size: 18rpx;
    padding: 3rpx 8rpx;
}

.item-content {
	position: relative;
	z-index: 3;
	padding: 20rpx;
	color: white;
}

.item-title-overlay {
	font-size: 30rpx;
	font-weight: bold;
	display: block;
	margin-bottom: 5rpx;
}

.item-category-overlay {
	font-size: 24rpx;
	opacity: 0.9;
	display: block;
}

.item-duration-overlay {
	position: absolute;
	top: 20rpx;
	left: 20rpx;
	font-size: 22rpx;
	background-color: rgba(0,0,0,0.3);
	padding: 3rpx 8rpx;
	border-radius: 6rpx;
}

.item-stats-overlay {
	position: relative;
	z-index: 3;
	padding: 0 20rpx 20rpx;
	display: flex;
	align-items: center;
	font-size: 22rpx;
	color: #f0f0f0;
}

/* Age Appropriate Section */
.section.age-section {
	margin-top: 40rpx;
}

.horizontal-scroll {
	width: 100%;
	white-space: nowrap;
}

.horizontal-list {
	display: inline-block; /* Allows scroll */
}

.horizontal-item {
	display: inline-block;
	width: 220rpx;
	height: 180rpx;
	background-color: #fff;
	border-radius: 20rpx;
	margin-right: 20rpx;
	position: relative;
	overflow: hidden;
	box-shadow: 0 4rpx 15rpx rgba(0, 0, 0, 0.04);
}

.item-image-placeholder {
	width: 100%;
	height: 110rpx;
	background-color: #e9ecf1; /* Placeholder color */
	border-radius: 20rpx 20rpx 0 0;
}

.item-title-horizontal {
	font-size: 26rpx;
	color: #333;
	padding: 10rpx 15rpx;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: normal;
}

/* Navbar Styles (Copied from home/profile) */
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
	font-size: 48rpx;
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

/* Icons for nav bar */
.icon-filled-home:before { content: "\e6b8"; } 
.icon-filled-chat:before { content: "\e65f"; } 
.icon-filled-discover:before { content: "\e68e"; }
.icon-filled-profile:before { content: "\e7ae"; }

/* Icons used in this page */
.icon-search:before { content: "\e61f"; }
.icon-signal:before { content: "\e609"; }
.icon-wifi:before { content: "\e7a1"; }
.icon-battery-full:before { content: "\e611"; }

</style> 