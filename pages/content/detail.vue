<template>
	<view class="detail-container">
		<!-- Header with image -->
		<view class="content-header">
			<image class="content-cover" :src="content.image" mode="aspectFill"></image>
			<view class="header-overlay"></view>
			<view class="back-button" @click="navigateBack">
				<text class="iconfont icon-left"></text>
			</view>
			<view class="favorite-button" @click="toggleFavorite">
				<text class="iconfont" :class="isFavorite ? 'icon-heart-fill' : 'icon-heart'"></text>
			</view>
		</view>
		
		<!-- Content Info -->
		<view class="content-info">
			<text class="content-tag">{{ content.tag }}</text>
			<text class="content-title">{{ content.title }}</text>
			<view class="content-meta">
				<text class="meta-item">{{ content.age }}</text>
				<text class="meta-item">{{ content.category }}</text>
				<text class="meta-item">{{ content.duration }}</text>
			</view>
			
			<!-- Action Buttons -->
			<view class="action-buttons">
				<button 
					class="primary-btn" 
					@click="startContent"
				>{{ actionButtonText }}</button>
				<button 
					class="secondary-btn" 
					@click="downloadContent"
				>
					<text class="iconfont icon-download"></text>
					<text>下载</text>
				</button>
			</view>
			
			<!-- Description -->
			<view class="description-section">
				<text class="section-title">内容简介</text>
				<text class="description-text">{{ content.description }}</text>
			</view>
			
			<!-- Learning Goals -->
			<view class="learning-section" v-if="content.learningGoals && content.learningGoals.length > 0">
				<text class="section-title">学习目标</text>
				<view class="learning-goals">
					<view v-for="(goal, index) in content.learningGoals" :key="index" class="learning-goal">
						<text class="iconfont icon-check"></text>
						<text>{{ goal }}</text>
					</view>
				</view>
			</view>
			
			<!-- Preview -->
			<view class="preview-section" v-if="content.preview">
				<text class="section-title">预览</text>
				<view class="preview-content">
					<image v-if="content.previewType === 'image'" class="preview-image" :src="content.preview" mode="aspectFill"></image>
					<view v-else-if="content.previewType === 'audio'" class="preview-audio">
						<text class="iconfont icon-play-circle"></text>
						<view class="audio-progress">
							<view class="audio-progress-bar" style="width: 30%;"></view>
						</view>
						<text class="audio-time">0:30 / 1:30</text>
					</view>
					<view v-else class="preview-video">
						<image class="video-thumbnail" :src="content.preview" mode="aspectFill"></image>
						<view class="video-play-button">
							<text class="iconfont icon-play"></text>
						</view>
					</view>
				</view>
			</view>
			
			<!-- Related Content -->
			<view class="related-section">
				<text class="section-title">相关推荐</text>
				<scroll-view class="related-scroll" scroll-x="true">
					<view v-for="(item, index) in relatedContent" :key="index" class="related-item" @click="navigateToDetail(item.id, item.type)">
						<image class="related-image" :src="item.image" mode="aspectFill"></image>
						<text class="related-title">{{ item.title }}</text>
						<text class="related-meta">{{ item.meta }}</text>
					</view>
				</scroll-view>
			</view>
		</view>
		
		<!-- Floating Player (shown when content is playing) -->
		<view class="floating-player" v-if="isPlaying">
			<view class="player-info">
				<image class="player-thumbnail" :src="content.image" mode="aspectFill"></image>
				<view class="player-text">
					<text class="player-title">{{ content.title }}</text>
					<view class="player-progress">
						<view class="progress-bar">
							<view class="progress-filled" :style="{ width: playProgress + '%' }"></view>
						</view>
						<text class="progress-time">{{ formatTime(currentTime) }} / {{ formatTime(totalTime) }}</text>
					</view>
				</view>
			</view>
			<view class="player-controls">
				<text class="iconfont icon-pause" @click="togglePlay"></text>
				<text class="iconfont icon-close" @click="stopPlay"></text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			id: 0,
			type: '',
			content: {
				id: 1,
				title: '小兔子的奇幻冒险',
				description: '这是一个关于小兔子在森林里探险的故事。小兔子勇敢地走出家门，遇到了各种各样的动物朋友，他们一起解决了很多问题，学习了友谊和勇气的重要性。这个故事将教会孩子勇敢面对挑战，与朋友合作的重要性。',
				age: '适合3-5岁',
				category: '睡前故事',
				duration: '10分钟',
				image: '/static/images/story1.jpg',
				tag: '推荐',
				learningGoals: [
					'培养想象力和创造力',
					'学习友谊和合作的重要性',
					'增强语言理解和表达能力'
				],
				preview: '/static/images/story1-preview.jpg',
				previewType: 'image'
			},
			relatedContent: [
				{
					id: 2,
					title: '勇敢的小火车',
					image: '/static/images/story2.jpg',
					meta: '故事 | 8分钟',
					type: 'story'
				},
				{
					id: 3,
					title: '小鸟学飞行',
					image: '/static/images/story3.jpg',
					meta: '故事 | 12分钟',
					type: 'story'
				},
				{
					id: 101,
					title: '动物朋友认知游戏',
					image: '/static/images/game1.jpg',
					meta: '游戏 | 互动',
					type: 'game'
				}
			],
			isFavorite: false,
			isPlaying: false,
			playProgress: 0,
			currentTime: 0,
			totalTime: 600, // 10 minutes in seconds
			playTimer: null
		}
	},
	computed: {
		actionButtonText() {
			switch(this.type) {
				case 'story':
					return '开始听故事';
				case 'game':
					return '开始游戏';
				case 'learn':
					return '开始学习';
				case 'activity':
					return '查看活动';
				default:
					return '开始';
			}
		}
	},
	onLoad(options) {
		// Get parameters from URL
		if (options.id) {
			this.id = parseInt(options.id);
		}
		if (options.type) {
			this.type = options.type;
		}
		
		// In a real app, we would fetch the content based on id and type
		// For this example, we're using static content
		this.loadContent();
	},
	methods: {
		loadContent() {
			// In a real app, this would be an API call based on this.id and this.type
			// For now, we use the default content in data
			console.log('Loading content with ID: ' + this.id + ' and type: ' + this.type);
		},
		navigateBack() {
			uni.navigateBack();
		},
		toggleFavorite() {
			this.isFavorite = !this.isFavorite;
			
			uni.showToast({
				title: this.isFavorite ? '已添加到收藏' : '已取消收藏',
				icon: 'none'
			});
		},
		startContent() {
			this.isPlaying = true;
			
			// Simulate content playback
			this.currentTime = 0;
			this.playProgress = 0;
			
			// Clear any existing timer
			if (this.playTimer) {
				clearInterval(this.playTimer);
			}
			
			// Update progress every second
			this.playTimer = setInterval(() => {
				if (this.currentTime < this.totalTime) {
					this.currentTime += 1;
					this.playProgress = (this.currentTime / this.totalTime) * 100;
				} else {
					this.stopPlay();
				}
			}, 1000);
		},
		downloadContent() {
			uni.showLoading({
				title: '下载中...'
			});
			
			// Simulate download
			setTimeout(() => {
				uni.hideLoading();
				uni.showToast({
					title: '下载完成',
					icon: 'success'
				});
			}, 2000);
		},
		navigateToDetail(id, type) {
			uni.navigateTo({
				url: `/pages/content/detail?id=${id}&type=${type}`
			});
		},
		togglePlay() {
			// In a real app, this would pause/play the content
			uni.showToast({
				title: '播放暂停',
				icon: 'none'
			});
		},
		stopPlay() {
			this.isPlaying = false;
			
			if (this.playTimer) {
				clearInterval(this.playTimer);
				this.playTimer = null;
			}
		},
		formatTime(seconds) {
			const minutes = Math.floor(seconds / 60);
			const remainingSeconds = Math.floor(seconds % 60);
			return `${minutes}:${remainingSeconds < 10 ? '0' : ''}${remainingSeconds}`;
		}
	},
	onUnload() {
		// Make sure to clear the timer when page is unloaded
		if (this.playTimer) {
			clearInterval(this.playTimer);
			this.playTimer = null;
		}
	}
}
</script>

<style>
.detail-container {
	background-color: #f5f5f5;
	min-height: 100vh;
}

.content-header {
	position: relative;
	height: 500rpx;
}

.content-cover {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.header-overlay {
	position: absolute;
	bottom: 0;
	left: 0;
	right: 0;
	height: 200rpx;
	background: linear-gradient(to bottom, rgba(0,0,0,0), rgba(0,0,0,0.5));
}

.back-button, .favorite-button {
	position: absolute;
	top: 40rpx;
	width: 80rpx;
	height: 80rpx;
	background-color: rgba(0,0,0,0.3);
	border-radius: 40rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	color: white;
	font-size: 32rpx;
}

.back-button {
	left: 30rpx;
}

.favorite-button {
	right: 30rpx;
}

.content-info {
	padding: 40rpx 30rpx;
	margin-top: -50rpx;
	border-radius: 30rpx 30rpx 0 0;
	background-color: #fff;
	position: relative;
}

.content-tag {
	display: inline-block;
	background-color: #4a90e2;
	color: white;
	font-size: 24rpx;
	padding: 6rpx 20rpx;
	border-radius: 20rpx;
	margin-bottom: 20rpx;
}

.content-title {
	font-size: 40rpx;
	font-weight: 600;
	margin-bottom: 20rpx;
	display: block;
}

.content-meta {
	display: flex;
	margin-bottom: 30rpx;
}

.meta-item {
	font-size: 28rpx;
	color: #6c757d;
	margin-right: 30rpx;
}

.action-buttons {
	display: flex;
	margin-bottom: 40rpx;
}

.primary-btn, .secondary-btn {
	flex: 1;
	height: 90rpx;
	border-radius: 45rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 32rpx;
	font-weight: 500;
}

.primary-btn {
	background-color: #4a90e2;
	color: white;
	margin-right: 20rpx;
}

.secondary-btn {
	background-color: white;
	color: #4a90e2;
	border: 1rpx solid #4a90e2;
	display: flex;
	align-items: center;
}

.secondary-btn .iconfont {
	margin-right: 10rpx;
}

.section-title {
	font-size: 32rpx;
	font-weight: 600;
	margin-bottom: 20rpx;
	display: block;
}

.description-section {
	margin-bottom: 40rpx;
}

.description-text {
	font-size: 28rpx;
	line-height: 1.6;
	color: #212529;
}

.learning-section {
	margin-bottom: 40rpx;
}

.learning-goals {
	display: flex;
	flex-direction: column;
}

.learning-goal {
	display: flex;
	align-items: center;
	margin-bottom: 16rpx;
	font-size: 28rpx;
}

.learning-goal .iconfont {
	color: #28a745;
	margin-right: 16rpx;
}

.preview-section {
	margin-bottom: 40rpx;
}

.preview-image {
	width: 100%;
	height: 400rpx;
	border-radius: 16rpx;
	object-fit: cover;
}

.preview-audio {
	background-color: #f8f9fa;
	border-radius: 16rpx;
	padding: 30rpx;
	display: flex;
	align-items: center;
}

.preview-audio .iconfont {
	font-size: 60rpx;
	color: #4a90e2;
	margin-right: 20rpx;
}

.audio-progress {
	flex: 1;
	height: 10rpx;
	background-color: #e9ecef;
	border-radius: 5rpx;
	margin-right: 20rpx;
	overflow: hidden;
}

.audio-progress-bar {
	height: 100%;
	background-color: #4a90e2;
}

.audio-time {
	font-size: 24rpx;
	color: #6c757d;
}

.preview-video {
	position: relative;
	width: 100%;
	height: 400rpx;
	border-radius: 16rpx;
	overflow: hidden;
}

.video-thumbnail {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.video-play-button {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	width: 100rpx;
	height: 100rpx;
	background-color: rgba(0,0,0,0.5);
	border-radius: 50%;
	display: flex;
	align-items: center;
	justify-content: center;
}

.video-play-button .iconfont {
	color: white;
	font-size: 50rpx;
}

.related-section {
	margin-bottom: 60rpx;
}

.related-scroll {
	white-space: nowrap;
}

.related-item {
	display: inline-block;
	width: 280rpx;
	margin-right: 20rpx;
}

.related-image {
	width: 280rpx;
	height: 180rpx;
	border-radius: 16rpx;
	margin-bottom: 10rpx;
}

.related-title {
	font-size: 28rpx;
	font-weight: 500;
	margin-bottom: 6rpx;
	display: block;
	white-space: normal;
	overflow: hidden;
	text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	height: 76rpx;
}

.related-meta {
	font-size: 24rpx;
	color: #6c757d;
}

.floating-player {
	position: fixed;
	bottom: 0;
	left: 0;
	right: 0;
	background-color: white;
	padding: 20rpx 30rpx;
	display: flex;
	align-items: center;
	justify-content: space-between;
	box-shadow: 0 -2rpx 10rpx rgba(0,0,0,0.1);
	z-index: 100;
}

.player-info {
	display: flex;
	align-items: center;
	flex: 1;
}

.player-thumbnail {
	width: 100rpx;
	height: 100rpx;
	border-radius: 12rpx;
	margin-right: 20rpx;
}

.player-text {
	flex: 1;
}

.player-title {
	font-size: 28rpx;
	font-weight: 500;
	margin-bottom: 10rpx;
	display: block;
}

.player-progress {
	display: flex;
	flex-direction: column;
}

.progress-bar {
	height: 6rpx;
	background-color: #e9ecef;
	border-radius: 3rpx;
	margin-bottom: 6rpx;
	overflow: hidden;
}

.progress-filled {
	height: 100%;
	background-color: #4a90e2;
}

.progress-time {
	font-size: 22rpx;
	color: #6c757d;
}

.player-controls {
	display: flex;
}

.player-controls .iconfont {
	font-size: 40rpx;
	padding: 20rpx;
	color: #212529;
}
</style> 